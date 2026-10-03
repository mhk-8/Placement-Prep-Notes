
# C++ STL — One Pager

> **Use:** your primary contest/OA language. This is the API recall sheet, not a tutorial.
> The complexity column is what interviewers actually probe.

---

## 1. The boilerplate ⭐

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
int main(){
    ios::sync_with_stdio(false); cin.tie(nullptr);   // ⭐ 5-10x faster I/O. Never omit in an OA
    int n; cin >> n;
    vector<ll> a(n); for (auto &x : a) cin >> x;
    cout << ans << "\n";                              // "\n" not endl — endl flushes every time ⚠️
}
```

---

## 2. Containers

| Container | Underlying | Access | Insert | Erase | Ordered | Notes |
|---|---|---|---|---|---|---|
| `vector` | dynamic array | O(1) | O(1) am. back | O(n) middle | insertion | default choice ⭐ |
| `deque` | chunked array | O(1) | O(1) both ends | O(n) middle | insertion | sliding-window max |
| `list` | doubly linked | O(n) | O(1) at iterator | O(1) | insertion | `splice` is O(1) ⭐ LRU |
| `forward_list` | singly linked | O(n) | O(1) | O(1) | insertion | rarely needed |
| `array<T,N>` | fixed stack array | O(1) | — | — | insertion | no heap allocation |
| `set` / `multiset` | red-black tree | — | O(log n) | O(log n) | **sorted** | `lower_bound` member ⭐ |
| `map` / `multimap` | red-black tree | O(log n) | O(log n) | O(log n) | **sorted by key** | |
| `unordered_set/map` | hash table | O(1) avg, O(n) worst | O(1) avg | O(1) avg | ❌ | ⚠️ hackable in contests |
| `priority_queue` | binary heap on vector | O(1) top | O(log n) | O(log n) pop | **max-heap by default** | |
| `stack` / `queue` | deque adaptor | O(1) | O(1) | O(1) | — | |
| `bitset<N>` | packed bits | O(1) | — | — | — | ⭐ /64 speedup on subset-sum |
| `string` | dynamic char array | O(1) | O(1) am. | O(n) | — | |

### The gotchas ⚠️
```cpp
priority_queue<int> maxh;                                              // MAX-heap (default)
priority_queue<int, vector<int>, greater<int>> minh;                   // MIN-heap ⭐ remember this
vector<bool>                      // NOT a real container of bool — proxy references. Use
                                  // vector<char> or bitset if you need references or speed ⚠️
map vs unordered_map              // map is ordered + guaranteed O(log n); unordered_map is
                                  // O(1) average but O(n) adversarial. In an OA, map is safer
                                  // if the test data might be anti-hash. ⭐
set<int>::lower_bound(x)          // O(log n) — the MEMBER function
std::lower_bound(s.begin(),...)   // O(n) on a set! ⚠️ the free function walks the iterators
```

---

## 3. Algorithms (`<algorithm>`, `<numeric>`)

| Call | Does | Complexity |
|---|---|---|
| `sort(b,e)` / `sort(b,e,cmp)` | introsort, **not stable** | O(n log n) |
| `stable_sort(b,e)` | merge sort | O(n log n), O(n) space |
| `partial_sort(b, b+k, e)` | smallest k sorted at front | O(n log k) |
| `nth_element(b, b+k, e)` | k-th in place, partition around it | **O(n)** avg ⭐ |
| `lower_bound(b,e,x)` | first `>= x` | O(log n) on random access |
| `upper_bound(b,e,x)` | first `> x` | O(log n) |
| `equal_range(b,e,x)` | the `[lower, upper)` pair | O(log n) |
| `binary_search(b,e,x)` | bool only | O(log n) |
| `unique(b,e)` | collapse **adjacent** duplicates; returns new end | O(n) ⚠️ sort first |
| `reverse` / `rotate` | | O(n) |
| `next_permutation(b,e)` | next lexicographic; false when wrapping | O(n) ⭐ |
| `accumulate(b,e,init)` | sum/fold | O(n) ⚠️ `init` type decides overflow: use `0LL` |
| `partial_sum` / `exclusive_scan` | prefix sums | O(n) |
| `max_element` / `min_element` / `minmax_element` | iterators | O(n) |
| `count` / `count_if` / `find` / `find_if` | | O(n) |
| `all_of` / `any_of` / `none_of` | | O(n) |
| `fill` / `iota` | `iota` writes 0,1,2,… ⭐ union-find init | O(n) |
| `__gcd(a,b)` / `gcd`,`lcm` (C++17) | | O(log) |
| `__builtin_popcount(x)` / `popcountll` | set bits ⭐ bitmask DP | O(1) |
| `__builtin_clz` / `ctz` | leading / trailing zeros ⚠️ undefined for 0 | O(1) |

### The erase-remove idiom
```cpp
v.erase(remove(v.begin(), v.end(), val), v.end());    // remove() only shuffles; erase() shrinks
// C++20: std::erase(v, val);
```

### Dedupe
```cpp
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());   // ⭐ the two-line idiom; sort is mandatory
```

---

## 4. Custom comparators ⭐

```cpp
// 1. lambda for sort
sort(v.begin(), v.end(), [](const P& a, const P& b){
    return a.second != b.second ? a.second > b.second : a.first < b.first;  // by 2nd desc, 1st asc
});

// 2. struct for a container's ORDERING type (must be strict weak ordering)
struct Cmp { bool operator()(const P& a, const P& b) const { return a.second < b.second; } };
set<P, Cmp> s;
priority_queue<P, vector<P>, Cmp> pq;   // ⚠️ Cmp "less" gives a MAX-heap

// 3. operator< on your own type
struct Node { int d, v; bool operator<(const Node& o) const { return d > o.d; } };  // min-heap
```

> ⚠️ **A comparator must be a strict weak ordering.** `return a <= b;` is the classic bug — it
> makes `sort` read out of bounds and crash. Use `<`, never `<=`.

---

## 5. Strings

```cpp
s.substr(pos, len)            s.find("ab")  // npos if absent
s.find_first_of("aeiou")      stoi/stoll/stod(s)        to_string(x)
s += c;  s.push_back(c);      s.append(k, 'x')
transform(s.begin(), s.end(), s.begin(), ::tolower);
stringstream ss(line); string tok; while (ss >> tok) { ... }         // ⭐ tokenise by space
getline(cin, line);           // ⚠️ after cin >> n, consume the newline first: cin.ignore();
```

---

## 6. Iterators and C++11-17 you should use ⭐

```cpp
for (auto& [k, v] : mp) { ... }                   // structured bindings (C++17)
auto it = mp.find(k); if (it != mp.end()) ...
mp.emplace(k, v);  mp.try_emplace(k, v);          // avoids constructing on hit
auto [it, ok] = s.insert(x);                      // ok == false if already present
tie(a, b) = make_pair(b, a);
vector<pair<int,int>> v; v.emplace_back(1, 2);    // no temporary
optional<int>, variant<...>, string_view          // mention these if asked "modern C++"
```

---

## 7. Interview-level C++ semantics ⭐⭐ (Adobe, Qualcomm, NVIDIA all ask)

| Concept | One-line answer |
|---|---|
| `struct` vs `class` | Only default access: `public` vs `private` |
| Virtual function | Dynamic dispatch via a per-class vtable; object holds a vptr |
| **Virtual destructor** | Required in any base deleted through a base pointer, else UB/leak ⚠️ |
| Pure virtual / abstract | `= 0`; class cannot be instantiated |
| `override` / `final` | Compiler-checked intent; use `override` always |
| Static vs dynamic binding | Non-virtual resolved at compile time; virtual at run time |
| **Rule of 0/3/5** | 0: own no resources. 3: dtor, copy ctor, copy assign. 5: + move ctor, move assign |
| **lvalue vs rvalue** | lvalue has an identity/address; rvalue is a temporary |
| `std::move` | A **cast** to rvalue reference. It moves nothing by itself ⭐ |
| Copy elision / RVO | Compiler constructs the return value in place; mandatory in C++17 for prvalues |
| `unique_ptr` / `shared_ptr` / `weak_ptr` | Sole ownership / ref-counted / non-owning observer, breaks cycles ⚠️ |
| `shared_ptr` cost | Atomic refcount increments — not free; a control block per object |
| `const` member function | Promises not to modify `*this`; part of overload resolution |
| `mutable` | Field modifiable even in a const method (caches, mutexes) |
| `explicit` | Blocks implicit single-argument conversions |
| `static` in a class / function | Shared across instances / retains value across calls |
| Templates | Compile-time instantiation; code bloat, but zero run-time dispatch cost |
| **Shallow vs deep copy** | Default copy is member-wise; raw pointer members alias ⚠️ double-free |
| Memory layout | stack · heap · static/global · code; plus padding/alignment in structs |
| `new` vs `malloc` | `new` calls constructors, is type-safe, throws; `malloc` returns void*, no ctor |
| Dangling vs leak | Pointer to freed memory vs memory with no pointer to it |
| `volatile` | Forbids caching the value in a register — MMIO and signal handlers, **not** threading ⭐ |
| Memory order / `std::atomic` | Thread-safe primitives; `volatile` is not a concurrency tool |
| Undefined behaviour examples | Signed overflow, OOB access, use-after-free, `a[i++] = i` |

### Output-prediction traps ⚠️ (Adobe loves these)
```cpp
int i = 5; cout << i++ << " " << ++i;          // unspecified evaluation order — don't rely on it
int a[] = {1,2,3}; cout << *(a+1) << a[1];     // same thing: 2 2
char c = 'A' + 1;                              // 'B'
cout << (0.1 + 0.2 == 0.3);                    // 0  ⚠️ floating point
int x = 7/2, y = -7/2;                         // 3, -3 (truncation toward zero in C++11+)
cout << sizeof('a') << sizeof("a");            // C++: 1 and 2 (the NUL byte) ⭐
static int s;                                  // zero-initialised; a local non-static int is NOT
delete p; delete p;                            // double free — UB
Base* b = new Derived; delete b;               // ⚠️ leak/UB unless ~Base is virtual
```

---

## 8. Performance notes (relevant to your CUDA/systems profile) ⭐

```
reserve() before a known-size push_back loop  → avoids repeated reallocation + copies
vector<T> over list<T> almost always          → cache locality dominates asymptotic advantages
struct-of-arrays over array-of-structs        → for vectorisation and coalesced access
pass by const reference for anything > 16 B   → avoid silent copies
emplace_back over push_back(T(...))           → constructs in place
bitset for dense boolean DP                   → 64x fewer operations ⭐ subset sum
```

---

## Recall questions
1. How do you make a min-heap with `priority_queue`? Write the declaration.
2. Why is `std::lower_bound` the wrong call on a `std::set`?
3. What is wrong with a comparator that returns `a <= b`?
4. What does `std::move` actually do?
5. When is a virtual destructor mandatory, and what happens without one?
6. `vector<bool>` — why is it not a container of `bool`?
7. What is `volatile` for, and what is it *not* for?
