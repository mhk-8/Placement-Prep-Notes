# Consistent Hashing

> A small, elegant idea that appears in almost every distributed system: caches, sharded databases, load balancers, CDNs. It is also a favourite deep-dive question because it can be explained fully in five minutes and has a real proof of its benefit.

---

## 1. The problem it solves

The naive way to map keys to N nodes:

```
node = hash(key) mod N
```

This is uniform and fast, and it has one fatal property: **changing N remaps almost every key.**

Concretely, with 4 nodes and a key hashing to 1,234,567:
```
1,234,567 mod 4 = 3     → node 3
add a node:
1,234,567 mod 5 = 2     → node 2
```

Going from 4 to 5 nodes, the fraction of keys that keep their node is roughly **1/5** — about **80% of keys move**.

**Why this is catastrophic.** For a cache, 80% of entries are suddenly misses, so the origin receives a near-total load spike at exactly the moment you were adding capacity — the cure triggers the disease. For a sharded database, 80% of the data must physically move, which can take hours and saturate the network.

---

## 2. The hash ring

Map both **keys** and **nodes** onto the same circular space, typically 0 to 2³²−1.

- Hash each node's identifier to a point on the ring.
- Hash each key to a point on the ring.
- **A key belongs to the first node encountered walking clockwise from the key's position.**

```
                0 / 2^32
                    │
         Node A ────┼──── k1
              ╱     │      ╲
          k4 ╱             ╲ k2
            │               │
        Node C          Node B
            │               │
             ╲     k3      ╱
              ╲           ╱
               ──────────

     k1 → B   (first node clockwise)
     k2 → B
     k3 → C
     k4 → A
```

### Why this fixes the problem

**Adding a node** places it at one point on the ring. It takes over only the keys lying between it and its predecessor — everything else is untouched.

**Removing a node** hands its keys to its clockwise successor. Again, nothing else moves.

**On average only K/N keys move**, where K is the number of keys and N the number of nodes. Going from 4 nodes to 5 moves ~20% of keys instead of ~80%.

That ratio — **K/N instead of nearly all** — is the entire point, and it is the sentence to say in an interview.

---

## 3. Virtual nodes

The basic ring has two problems, both caused by placing each physical node at exactly one point:

1. **Uneven distribution.** Random placement of a few points on a circle produces uneven arc lengths. With 3 nodes, one might own 60% of the ring.
2. **Uneven redistribution on failure.** When a node dies, its **entire** range goes to one successor, which may then be handling double the load — potentially cascading.

**The fix:** give each physical node many positions on the ring, called **virtual nodes** or **vnodes**.

```
hash("nodeA#1"), hash("nodeA#2"), ... hash("nodeA#150")
```

With 100–200 vnodes per physical node:
- The law of large numbers smooths the arcs, so load variance drops to a few percent.
- When a node fails, its many small ranges are absorbed by **many different successors**, spreading the extra load instead of concentrating it.
- **Heterogeneous hardware is handled naturally** — a machine with twice the capacity is given twice as many vnodes.

**This is the detail that separates a memorised answer from an understood one.** The plain ring is a nice idea; vnodes are what make it usable, and knowing *why* (variance and failure redistribution) is the thing being probed.

**The cost:** the ring metadata grows (N × vnodes entries), and lookup is a binary search over that sorted structure — O(log(N × vnodes)), still trivial.

---

## 4. Implementation

```python
import bisect, hashlib

class ConsistentHashRing:
    def __init__(self, nodes=None, vnodes=150):
        self.vnodes = vnodes
        self.ring = {}          # hash -> physical node
        self.sorted_keys = []   # sorted hashes, for binary search
        for n in (nodes or []):
            self.add_node(n)

    def _hash(self, key):
        return int(hashlib.md5(key.encode()).hexdigest(), 16)

    def add_node(self, node):
        for i in range(self.vnodes):
            h = self._hash(f"{node}#{i}")
            self.ring[h] = node
            bisect.insort(self.sorted_keys, h)

    def remove_node(self, node):
        for i in range(self.vnodes):
            h = self._hash(f"{node}#{i}")
            del self.ring[h]
            self.sorted_keys.remove(h)

    def get_node(self, key):
        if not self.ring:
            return None
        h = self._hash(key)
        idx = bisect.bisect_right(self.sorted_keys, h)   # first vnode clockwise
        if idx == len(self.sorted_keys):
            idx = 0                                       # wrap around the ring
        return self.ring[self.sorted_keys[idx]]

    def get_nodes(self, key, count):
        # The first `count` DISTINCT physical nodes clockwise -- for replication.
        if not self.ring:
            return []
        h = self._hash(key)
        idx = bisect.bisect_right(self.sorted_keys, h)
        out, seen = [], set()
        for i in range(len(self.sorted_keys)):
            node = self.ring[self.sorted_keys[(idx + i) % len(self.sorted_keys)]]
            if node not in seen:
                seen.add(node)
                out.append(node)
                if len(out) == count:
                    break
        return out
```

**`get_nodes` is how replication works on a ring:** a key's N replicas are the next N **distinct** physical nodes clockwise. The distinctness check matters — without it, several vnodes of the same machine would be chosen and the replicas would not be independent.

**Two implementation details worth mentioning:**
- The hash function should be fast and well-distributed. MD5 is used above for clarity; production systems use MurmurHash or xxHash, which are faster and non-cryptographic (no security property is needed here).
- `bisect_right` plus wraparound is the whole lookup. The wraparound case — a key hashing past the last vnode — is the off-by-one people get wrong.

---

## 5. Where it is used

| System | Use |
|---|---|
| **Memcached clients** | choose which cache server holds a key |
| **Cassandra** | partition data across the cluster (with vnodes) |
| **DynamoDB** | the original Dynamo paper's partitioning scheme |
| **Riak** | ring-based partitioning |
| **CDNs** | map a URL to an edge cache so the same object is cached once |
| **Load balancers** | session affinity without stickiness state |
| **Envoy / Maglev** | consistent routing to upstream hosts |

---

## 6. Limitations, and what to say about them

**Hot keys are unsolved.** Consistent hashing distributes *keys* evenly; it does nothing about one key receiving a million requests per second. That key lands on one node regardless. Mitigations are elsewhere: a local cache in front, salting the key, or special-casing it.

**Range queries are lost.** Hashing destroys ordering, so "all keys between X and Y" becomes a scan of every node. Systems that need both (Cassandra) hash the *partition* key and keep a sorted *clustering* key within the partition — getting distribution across the cluster and ordering within it.

**Ring metadata must be consistent.** Every client or router needs the same view of which nodes exist. In practice this lives in a consensus store (ZooKeeper, etcd) or is gossiped between nodes (Cassandra). A stale view sends requests to the wrong node — so this is a component to include in the diagram, not an afterthought.

**Data movement is still real.** K/N is far better than K, but on a 100 TB cluster it is still terabytes, and it must be throttled so rebalancing does not saturate the network during an incident.

---

## 7. The alternative: fixed partitions

Worth knowing because several major systems prefer it.

Create many more partitions than nodes — say **1,024 partitions across 10 nodes** — and assign whole partitions to nodes. Adding a node means moving some partitions to it; the partition count never changes, so no key is ever rehashed.

| | Consistent hashing | Fixed partitions |
|---|---|---|
| Key → partition | hash onto a ring | `hash(key) mod P`, P fixed forever |
| Partition → node | implicit from the ring | an explicit assignment map |
| Adding a node | takes over ring ranges | receives whole partitions |
| Rebalancing control | implicit | **explicit and easy to reason about** |
| Used by | Cassandra, Dynamo, Memcached clients | Elasticsearch, Riak (core), Kafka |

**Fixed partitioning is often simpler operationally** — you can see exactly which partitions live where and move them deliberately. Its constraint is that P must be chosen up front and generously, because changing it *is* a full reshuffle.

---

## 8. Recall questions

1. Why does `hash(key) mod N` fail when N changes? Give the fraction of keys that move going from 4 to 5 nodes.
2. Why is that catastrophic specifically for a cache?
3. Describe the ring construction and the lookup rule.
4. How many keys move on average when a node is added or removed, and why?
5. Give the two problems with a plain ring that virtual nodes solve.
6. Why do vnodes help specifically when a node **fails**?
7. How do vnodes accommodate heterogeneous hardware?
8. How is replication implemented on the ring, and what is the subtlety in choosing the replicas?
9. Which off-by-one case do implementations usually get wrong?
10. Why is a non-cryptographic hash function appropriate here?
11. Does consistent hashing solve hot keys? What does?
12. Why are range queries lost, and how does Cassandra get both?
13. Where does the ring metadata live, and what happens if a client's view is stale?
14. Compare consistent hashing with fixed partitioning, and say when each is preferable.
