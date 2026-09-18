# LLD Case Study — Rate Limiter and LRU Cache

> Two compact designs that appear constantly, both as standalone LLD questions and as components inside larger ones. They are short enough to implement fully in 30 minutes, which means the bar is correctness and clean extensibility rather than scope.

---

# Part A — Rate Limiter

## 1. Requirements

- Decide whether a request from a given client is allowed.
- Support **several algorithms** (fixed window, sliding window, token bucket) selectable per client or per endpoint.
- Support **different limits per tier** (free, premium).
- Thread-safe under concurrent calls.
- The check must be fast — it is on every request path.

**Out of scope:** distribution across servers (discussed in follow-ups), persistence.

## 2. Design

The varying behaviour is **the algorithm**, so it goes behind an interface — Strategy. The varying **configuration** is per client or tier, so it is looked up rather than hard-coded.

```
   ┌─────────────────────┐        ┌───────────────────────┐
   │  RateLimiter        │───────►│ <<interface>>         │
   │  (facade)           │        │  RateLimitAlgorithm   │
   └──────────┬──────────┘        └───────────┬───────────┘
              │ uses                          △
              ▼                 ┌─────────────┼──────────────┐
   ┌─────────────────────┐  FixedWindow  SlidingWindowLog  TokenBucket
   │ <<interface>>       │
   │  RuleProvider       │   (limit + window per client/tier/endpoint)
   └─────────────────────┘
```

## 3. Implementation

```python
from abc import ABC, abstractmethod
from collections import deque, defaultdict
from dataclasses import dataclass
from threading import Lock
from typing import Dict, Deque, Optional
import time


@dataclass(frozen=True)
class Rule:
    limit: int              # requests allowed
    window_seconds: float   # per this window
    burst: Optional[int] = None   # token bucket capacity, if applicable


@dataclass(frozen=True)
class Decision:
    allowed: bool
    remaining: int
    retry_after: float = 0.0


class RateLimitAlgorithm(ABC):
    @abstractmethod
    def allow(self, key: str, rule: Rule, now: float) -> Decision: ...


class FixedWindowCounter(RateLimitAlgorithm):
    # Simplest; suffers the boundary burst (up to 2x the limit across a boundary).
    def __init__(self):
        self._counts: Dict[str, tuple] = {}       # key -> (window_start, count)
        self._lock = Lock()

    def allow(self, key, rule, now):
        with self._lock:
            window_start = now - (now % rule.window_seconds)
            start, count = self._counts.get(key, (window_start, 0))
            if start != window_start:
                start, count = window_start, 0
            if count >= rule.limit:
                self._counts[key] = (start, count)
                return Decision(False, 0, start + rule.window_seconds - now)
            self._counts[key] = (start, count + 1)
            return Decision(True, rule.limit - count - 1)


class SlidingWindowLog(RateLimitAlgorithm):
    # Exact, but memory is O(requests in the window) per key.
    def __init__(self):
        self._log: Dict[str, Deque[float]] = defaultdict(deque)
        self._lock = Lock()

    def allow(self, key, rule, now):
        with self._lock:
            q = self._log[key]
            cutoff = now - rule.window_seconds
            while q and q[0] <= cutoff:
                q.popleft()
            if len(q) >= rule.limit:
                return Decision(False, 0, q[0] + rule.window_seconds - now)
            q.append(now)
            return Decision(True, rule.limit - len(q))


class TokenBucket(RateLimitAlgorithm):
    # Allows bursts up to `burst` while bounding the long-run average rate.
    # Refill is computed lazily on access -- no background timer needed.
    def __init__(self):
        self._state: Dict[str, tuple] = {}         # key -> (tokens, last_refill)
        self._lock = Lock()

    def allow(self, key, rule, now):
        capacity = rule.burst or rule.limit
        refill_rate = rule.limit / rule.window_seconds
        with self._lock:
            tokens, last = self._state.get(key, (float(capacity), now))
            tokens = min(capacity, tokens + (now - last) * refill_rate)
            if tokens >= 1.0:
                self._state[key] = (tokens - 1.0, now)
                return Decision(True, int(tokens - 1.0))
            self._state[key] = (tokens, now)
            return Decision(False, 0, (1.0 - tokens) / refill_rate)


class RuleProvider(ABC):
    @abstractmethod
    def rule_for(self, key: str, endpoint: str) -> Rule: ...


class TieredRuleProvider(RuleProvider):
    TIERS = {
        "free":    Rule(limit=100,   window_seconds=3600, burst=10),
        "premium": Rule(limit=10000, window_seconds=3600, burst=200),
    }

    def __init__(self, tier_of: Dict[str, str]):
        self._tier_of = tier_of

    def rule_for(self, key, endpoint):
        return self.TIERS[self._tier_of.get(key, "free")]


class RateLimiter:
    def __init__(self, algorithm: RateLimitAlgorithm, rules: RuleProvider):
        self._algo = algorithm
        self._rules = rules

    def check(self, client_key: str, endpoint: str = "*") -> Decision:
        rule = self._rules.rule_for(client_key, endpoint)
        return self._algo.allow(f"{client_key}:{endpoint}", rule, time.time())
```

```python
limiter = RateLimiter(TokenBucket(), TieredRuleProvider({"user-1": "premium"}))
d = limiter.check("user-1", "/api/search")
print(d.allowed, d.remaining)
```

## 4. Design decisions

| Decision | Reason |
|---|---|
| Algorithm behind an interface | The whole point of the question: adding sliding-window-counter is a new class |
| `Rule` as a value object | Limits become data, not code, so tiers and per-endpoint overrides need no new types |
| `Decision` carries `remaining` and `retry_after` | The caller can populate `X-RateLimit-*` and `Retry-After` headers |
| Lazy refill in `TokenBucket` | No background timer, no per-key thread — O(1) state and O(1) work |
| A lock per algorithm instance | Simple and correct; a striped lock or per-key lock reduces contention if needed |
| Key is `client:endpoint` | Lets one limiter serve both global and per-endpoint limits |

## 5. Follow-ups

**"Make it work across many servers."** Move the state to Redis and make the read-modify-write atomic with a **Lua script** — otherwise two concurrent requests both read 99 and both proceed. Alternatively, give each server a local allowance synced periodically, trading exactness for a round trip per request.

**"What if Redis is down?"** **Fail open** for ordinary APIs (availability over protection) and **fail closed** for security-critical paths like login. Alert either way. Volunteering this choice is the signal.

**"Which algorithm would you actually pick?"** Token bucket for public APIs, because real clients are idle-then-bursty and it bounds the average while tolerating that shape. Sliding window counter when burst tolerance is unwanted. Fixed window only when the boundary burst genuinely does not matter.

*(The algorithms, their formulas and the distributed design are covered in depth in `01_Fundamentals/07-rate-limiting.md`.)*

---

# Part B — LRU Cache

## 1. Requirements

- `get(key)` and `put(key, value)`, both **O(1)**.
- Fixed capacity; evict the **least recently used** entry when full.
- Optional TTL per entry.
- Thread-safe.
- Extensible to other eviction policies (LFU, FIFO).

## 2. The core structure

**Hash map + doubly linked list**, with sentinel head and tail nodes.

- The **hash map** gives O(1) location of a node by key.
- The **doubly linked list** maintains recency order: most recent at the front, least recent at the back.
- **Doubly** linked is essential: unlinking an arbitrary node in O(1) requires its `prev` pointer.
- **Sentinels** remove every null check from the link/unlink code, which is where the bugs live.

```
  head(sentinel) ⇄ [k3] ⇄ [k1] ⇄ [k2] ⇄ tail(sentinel)
                   most                least
                   recent              recent   ← evict from here

  map: {k1: node, k2: node, k3: node}
```

## 3. Implementation

```python
from abc import ABC, abstractmethod
from threading import RLock
from typing import Any, Dict, Optional
import time


class _Node:
    __slots__ = ("key", "value", "expires_at", "prev", "next")
    def __init__(self, key=None, value=None, expires_at=None):
        self.key, self.value, self.expires_at = key, value, expires_at
        self.prev: Optional["_Node"] = None
        self.next: Optional["_Node"] = None


class EvictionPolicy(ABC):
    @abstractmethod
    def on_access(self, cache: "Cache", node: _Node) -> None: ...
    @abstractmethod
    def victim(self, cache: "Cache") -> Optional[_Node]: ...


class LRUPolicy(EvictionPolicy):
    def on_access(self, cache, node):
        cache._move_to_front(node)
    def victim(self, cache):
        last = cache._tail.prev
        return None if last is cache._head else last


class FIFOPolicy(EvictionPolicy):
    # Insertion order only: access does not change position.
    def on_access(self, cache, node):
        pass
    def victim(self, cache):
        last = cache._tail.prev
        return None if last is cache._head else last


class Cache:
    def __init__(self, capacity: int, policy: EvictionPolicy = None):
        if capacity <= 0:
            raise ValueError("capacity must be positive")
        self._capacity = capacity
        self._policy = policy or LRUPolicy()
        self._map: Dict[Any, _Node] = {}
        self._head, self._tail = _Node(), _Node()      # sentinels
        self._head.next, self._tail.prev = self._tail, self._head
        self._lock = RLock()
        self.hits = self.misses = 0

    # ---- list primitives ----
    def _unlink(self, node: _Node) -> None:
        node.prev.next, node.next.prev = node.next, node.prev

    def _push_front(self, node: _Node) -> None:
        node.next, node.prev = self._head.next, self._head
        self._head.next.prev = node
        self._head.next = node

    def _move_to_front(self, node: _Node) -> None:
        self._unlink(node)
        self._push_front(node)

    # ---- public API ----
    def get(self, key) -> Optional[Any]:
        with self._lock:
            node = self._map.get(key)
            if node is None:
                self.misses += 1
                return None
            if node.expires_at is not None and time.time() > node.expires_at:
                self._evict(node)              # lazily expire on access
                self.misses += 1
                return None
            self._policy.on_access(self, node)
            self.hits += 1
            return node.value

    def put(self, key, value, ttl: Optional[float] = None) -> None:
        with self._lock:
            expires = time.time() + ttl if ttl else None
            node = self._map.get(key)
            if node is not None:
                node.value, node.expires_at = value, expires
                self._policy.on_access(self, node)
                return
            if len(self._map) >= self._capacity:
                victim = self._policy.victim(self)
                if victim is not None:
                    self._evict(victim)
            node = _Node(key, value, expires)
            self._map[key] = node
            self._push_front(node)

    def _evict(self, node: _Node) -> None:
        self._unlink(node)
        del self._map[node.key]                # MUST remove from the map too

    @property
    def hit_ratio(self) -> float:
        total = self.hits + self.misses
        return self.hits / total if total else 0.0
```

```python
c = Cache(2)
c.put("a", 1); c.put("b", 2)
c.get("a")             # "a" becomes most recent
c.put("c", 3)          # evicts "b", the least recently used
print(c.get("b"), c.get("a"), c.get("c"))   # None 1 3
```

## 4. Design decisions

| Decision | Reason |
|---|---|
| Hash map + **doubly** linked list | O(1) lookup and O(1) unlink; a singly linked list would need an O(n) scan for `prev` |
| **Sentinel** head and tail | Removes every null check from link/unlink — where the bugs actually are |
| Eviction behind `EvictionPolicy` | LFU and FIFO become new classes; the cache itself never changes |
| **Lazy** TTL expiry on access | No background sweeper needed for correctness; add one only to reclaim memory |
| `_evict` deletes from **both** structures | Forgetting the map delete is the classic bug: a silent memory leak plus stale lookups |
| `RLock`, not `Lock` | Policy callbacks re-enter cache methods |
| Hit/miss counters exposed | Hit ratio is the metric that decides whether the cache is worth having |

## 5. Follow-ups

**"Implement LFU."** Keep a frequency per node and a map from frequency to a doubly linked list of nodes at that frequency, plus a `min_freq` counter. On access, move the node from list f to list f+1; if list `min_freq` empties, increment `min_freq`. Eviction takes the tail of list `min_freq`. All O(1). Ties within a frequency are broken by LRU, which is why each frequency bucket is itself an ordered list.

**"Make it thread-safe with better concurrency."** The single lock serialises everything. Options: **striped locking** (shard the cache by `hash(key) % 16`, each shard with its own lock and list), or a concurrent map with approximate LRU (sampling a few candidates rather than maintaining exact order) — which is what Redis actually does.

**"How does this become a distributed cache?"** Consistent hashing to place keys on nodes (`01_Fundamentals/08`), replication for availability, and an eviction policy per node. The LRU order becomes per-node rather than global, which is an accepted approximation.

**"Where is this used in the real world?"** Database buffer pools, CPU caches, CDN edges, and the `LinkedHashMap` access-order trick in Java that gives an LRU cache in six lines.

---

## Practice

- [ ] Implement the LRU cache from memory in 20 minutes, with sentinels
- [ ] Implement LFU with the frequency-bucket structure
- [ ] Add striped locking and measure the contention difference
- [ ] Implement a sliding-window-counter rate limit algorithm as a fourth strategy
- [ ] Write the Redis Lua script for the distributed token bucket
