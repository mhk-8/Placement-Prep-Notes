# C++ — STL Containers and Complexities

> The lookup table. Know it well enough that container choice is instant.

---

## 1. Sequence containers

| Container | Access | Insert/erase | Notes |
|---|---|---|---|
| `vector<T>` | O(1) | O(1) amortised push_back; **O(n)** middle | contiguous, cache-friendly — the default |
| `deque<T>` | O(1) | **O(1) both ends**; O(n) middle | not contiguous; slightly slower iteration |
| `list<T>` | O(n) | O(1) given an iterator | rarely worth it in an OA |
| `array<T,N>` | O(1) | — | fixed size, stack allocated |
| `string` | O(1) | O(1) amortised append | mutable, unlike Java/Python |

```cpp
v.push_back(x); v.pop_back(); v.back(); v.front();
v.size(); v.empty(); v.clear(); v.resize(n); v.reserve(n);
v.insert(v.begin()+i, x);   // O(n)
v.erase(v.begin()+i);       // O(n)
```

`v.reserve(n)` before a big push_back loop avoids repeated reallocation — a real constant-factor win on 10⁶ elements.

---

## 2. Associative containers

| Container | Lookup | Insert | Ordered? | Backing |
|---|---|---|---|---|
| `map<K,V>` | O(log n) | O(log n) | **yes** | red-black tree |
| `set<T>` | O(log n) | O(log n) | **yes** | red-black tree |
| `multiset<T>` | O(log n) | O(log n) | yes | duplicates allowed |
| `unordered_map<K,V>` | **O(1) avg**, O(n) worst | O(1) avg | no | hash table |
| `unordered_set<T>` | O(1) avg, O(n) worst | O(1) avg | no | hash table |

```cpp
mp[k] = v;                    // INSERTS a default value if k is absent
mp.count(k);                  // 0 or 1 — safe membership test
mp.find(k) != mp.end();       // safe, and gives you the iterator
mp.at(k);                     // throws if absent
mp.erase(k);

s.insert(x); s.erase(x); s.count(x);
*s.begin();                   // minimum
*s.rbegin();                  // maximum
s.lower_bound(x);             // MEMBER function — O(log n)
```

**Two traps worth internalising.**

`mp[k]` inside a membership test silently inserts an entry, which corrupts counts and inflates memory. Use `.count()` or `.find()`.

**Use the member `s.lower_bound(x)`, never the free `lower_bound(s.begin(), s.end(), x)`.** The free function assumes random-access iterators; on a tree it degrades to O(n). Same for `map`.

`multiset::erase(x)` removes **all** copies of x. To remove one: `s.erase(s.find(x))`.

---

## 3. Adapters

| Adapter | Backing | Operations |
|---|---|---|
| `stack<T>` | deque | `push`, `pop`, `top`, O(1) |
| `queue<T>` | deque | `push`, `pop`, `front`, `back`, O(1) |
| `priority_queue<T>` | vector + heap | `push`/`pop` O(log n), `top` O(1) |

```cpp
priority_queue<int> maxh;                                  // MAX-heap (default)
priority_queue<int, vector<int>, greater<int>> minh;       // min-heap
priority_queue<pii, vector<pii>, greater<pii>> pq;         // min by .first

pq.top(); pq.pop();          // pop() returns void — read top() FIRST
```

`st.top()` or `pq.top()` on an empty container is **undefined behaviour**, not an exception. Always check `.empty()`.

---

## 4. Algorithms (from `<algorithm>` and `<numeric>`)

| Call | Complexity | Notes |
|---|---|---|
| `sort` | O(n log n) | introsort — **not stable** |
| `stable_sort` | O(n log n) (O(n log²n) low memory) | merge sort, stable |
| `nth_element(b, b+k, e)` | **O(n) average** | quickselect — k-th element in place |
| `partial_sort(b, b+k, e)` | O(n log k) | the k smallest, in order |
| `lower_bound` / `upper_bound` | O(log n) | requires a **sorted** range |
| `binary_search` | O(log n) | returns only a bool |
| `equal_range` | O(log n) | both bounds in one call |
| `unique` | O(n) | removes **consecutive** duplicates — sort first |
| `reverse` / `rotate` | O(n) | |
| `next_permutation` | O(n) | lexicographic; start sorted for a full enumeration |
| `accumulate` | O(n) | pass `0LL` to avoid int overflow |
| `max_element` / `min_element` | O(n) | returns an iterator |
| `count` / `count_if` | O(n) | |
| `iota` | O(n) | fill with consecutive values |
| `__gcd` | O(log min) | |

**Use `nth_element` instead of a full sort whenever only the k-th element matters** — O(n) against O(n log n), and it is one line.

---

## 5. Choosing a container

| Requirement | Choice |
|---|---|
| Indexed access, append at the end | `vector` |
| Insert/remove at **both** ends | `deque` |
| Ordered set with `lower_bound` | `set` / `map` |
| Fast membership, no ordering needed | `unordered_set` / `unordered_map` |
| Duplicates with ordering | `multiset` |
| Repeated "smallest/largest remaining" | `priority_queue` |
| Keys are small dense integers | plain `vector` or array — faster than any map |
| k-th element only | `nth_element` |

**The default is `vector`.** Contiguous memory makes a linear scan of a vector dramatically faster than a traversal of a `list` or `set` of the same length, even though both are O(n).

---

## 6. Bit builtins

```cpp
__builtin_popcount(x)        // set bits in an int
__builtin_popcountll(x)      // ... in a long long
__builtin_clz(x)             // leading zeros; UNDEFINED for x == 0
__builtin_ctz(x)             // trailing zeros; UNDEFINED for x == 0
31 - __builtin_clz(x)        // floor(log2(x)) for x > 0
1LL << k                     // ALWAYS use LL for k >= 31
```

---

## 7. Constant-factor notes that decide TLE

- `vector<bool>` is a bit-packed specialisation — compact but slow per access, and it is **not** a real container of `bool`. Use `vector<char>` when speed matters.
- `unordered_map` is slower than `map` for small n and vulnerable to anti-hash tests; `mp.reserve(n)` helps.
- Reserve capacity before large insertion loops.
- Prefer `'\n'` to `endl`.
- Prefer indices to iterators in the innermost loop of a hot function.
- `%` and `/` are expensive; avoid them in tight loops where a subtraction works.
