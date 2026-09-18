# C++ — Syntax Reference

> The syntax you must type without thinking. Not a language tutorial — a recall sheet for OA speed.

---

## 1. The starter file

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using pii = pair<int,int>;
#define all(x) (x).begin(), (x).end()

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);

    int t; cin >> t;
    while (t--) solve();
    return 0;
}
```

`sync_with_stdio(false)` unties C++ streams from C's, and `cin.tie(nullptr)` stops `cin` flushing `cout` before every read. Together they make `cin`/`cout` competitive with `scanf`/`printf`. **Never use `endl` inside a loop** — it flushes every time; use `'\n'`.

`<bits/stdc++.h>` is a GCC extension that includes everything. It works on virtually every OA judge; if it fails, include `<iostream> <vector> <algorithm> <string> <map> <set> <queue> <stack> <cmath> <numeric>`.

---

## 2. Types and limits

| Type | Size | Range |
|---|---|---|
| `int` | 4 B | ±2.1 × 10⁹ |
| `long long` | 8 B | ±9.2 × 10¹⁸ |
| `unsigned int` | 4 B | 0 … 4.3 × 10⁹ |
| `double` | 8 B | ~15–16 significant digits |
| `long double` | 8–16 B | more, platform dependent |
| `char` | 1 B | −128 … 127 |

```cpp
const int INF  = 1e9;          // safe: INF + INF still fits in int
const ll  LINF = 1e18;
INT_MAX, LLONG_MAX             // from <climits>
numeric_limits<int>::max()     // from <limits>
```

**Use `1e9` rather than `INT_MAX` as your infinity**, so that `INF + something` does not overflow during a relaxation step.

---

## 3. Declarations and control flow

```cpp
vector<int> a(n);                        // n zeros
vector<int> b(n, -1);                    // n copies of -1
vector<vector<int>> g(n, vector<int>(m, 0));   // n × m grid
array<int, 5> c = {1,2,3,4,5};           // fixed size, stack allocated

for (int i = 0; i < n; i++) { }
for (int x : a) { }                      // by value — copies
for (int& x : a) x *= 2;                 // by reference — modifies
for (auto& [k, v] : mp) { }              // structured bindings (C++17)

if (auto it = mp.find(k); it != mp.end()) { }   // init-statement in if (C++17)
```

---

## 4. Functions, references and lambdas

```cpp
void f(vector<int>& v);          // by reference — no copy, can modify
void g(const vector<int>& v);    // by const reference — no copy, read only
int  h(int x);                   // by value — copies (fine for scalars)

auto cmp = [](const pii& a, const pii& b) { return a.second < b.second; };

int total = 0;
auto add = [&](int x) { total += x; };    // [&] captures by reference
auto snap = [=](int x) { return total + x; };  // [=] captures by value
```

**Always pass containers by reference.** Passing a `vector<int>` of 10⁵ elements by value copies it on every call — a silent O(n) per invocation.

**Recursive lambda** (needed when you want a local DFS):
```cpp
function<void(int,int)> dfs = [&](int u, int p) {
    for (int v : g[u]) if (v != p) dfs(v, u);
};
```

---

## 5. Strings

```cpp
string s = "hello";
s.size(); s.length();            // same thing
s += 'x';                        // amortised O(1) — mutable, unlike Java/Python
s.substr(pos, len);              // LENGTH, not end index — the classic slip
s.find("lo");                    // returns string::npos if absent
s.erase(pos, len);
s.insert(pos, "abc");
reverse(all(s));
sort(all(s));
stoi(s); stoll(s); stod(s);      // string → number
to_string(42);                   // number → string

s[i] - '0'                       // char digit → int
'a' + k                          // int → char (cast back: char('a' + k))
```

```cpp
string line;
getline(cin, line);              // reads a whole line INCLUDING spaces
// after cin >> x, call cin.ignore() before getline, or you read the leftover newline
```

---

## 6. Iterators

```cpp
v.begin(), v.end()               // end() is ONE PAST the last element
v.rbegin(), v.rend()             // reverse
it = v.begin() + 3;              // random access (vector, string, deque)
next(it), prev(it), advance(it, 3);
distance(v.begin(), it);         // index of it
*it                              // the element
```

`set`, `map` and `list` iterators are **bidirectional, not random access** — `it + 3` does not compile; use `next(it, 3)`, which is O(3).

---

## 7. Common patterns

```cpp
sort(all(a));                                   // ascending
sort(all(a), greater<int>());                   // descending
sort(all(v), [](auto& x, auto& y){ return x.second < y.second; });

reverse(all(a));
a.erase(unique(all(a)), a.end());               // dedupe — MUST sort first
int idx = lower_bound(all(a), x) - a.begin();   // first >= x
int cnt = upper_bound(all(a), x) - lower_bound(all(a), x);   // count of x

*max_element(all(a));  *min_element(all(a));
accumulate(all(a), 0LL);                        // 0LL — or it sums in int
count(all(a), x);
fill(all(a), 0);
iota(all(a), 1);                                // 1, 2, 3, ...
next_permutation(all(a));                       // in-place, lexicographic
__gcd(a, b);  __builtin_popcount(x);  __builtin_clz(x);
swap(a, b);
```

---

## 8. Structs and operator overloading

```cpp
struct Edge {
    int u, v, w;
    bool operator<(const Edge& o) const { return w < o.w; }  // enables sort()
};

struct Node {
    int cost, id;
    bool operator>(const Node& o) const { return cost > o.cost; }
};
priority_queue<Node, vector<Node>, greater<Node>> pq;        // min-heap
```

A comparator must return **strict** less-than. Returning `<=` violates strict weak ordering and can make `std::sort` read out of bounds and crash — a real segfault, not a wrong answer.

---

## 9. Input and output

```cpp
int n; cin >> n;
vector<int> a(n);
for (auto& x : a) cin >> x;                  // note the &

for (int x : a) cout << x << ' ';
cout << '\n';

cout << fixed << setprecision(6) << ans << '\n';   // <iomanip>

while (cin >> x) { }                         // read until EOF
```

---

## 10. Memory and modern C++

```cpp
unique_ptr<Node> p = make_unique<Node>();    // sole ownership, auto-freed
shared_ptr<Node> q = make_shared<Node>();    // reference counted
// raw new/delete is almost never needed in an OA
```

**RAII:** resources are released by destructors on scope exit, including during exception unwinding — which is why C++ has no `finally`.

**Move semantics:** `std::move(v)` transfers ownership instead of copying, turning an O(n) copy into O(1). Relevant when returning large containers, though modern compilers elide most of these anyway.
