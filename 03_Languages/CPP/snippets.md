# C++ — Snippets

> Copy-ready blocks. Type each one from memory until it is automatic.

---

## 1. Full starter template

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using pii = pair<int,int>;
#define all(x) (x).begin(), (x).end()
const int INF = 1e9;
const ll  LINF = 1e18;
const int MOD = 1e9 + 7;

void solve() {
    int n; cin >> n;
    vector<int> a(n);
    for (auto& x : a) cin >> x;
    // ...
    cout << "\n";
}

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    int t = 1;
    // cin >> t;
    while (t--) solve();
    return 0;
}
```

---

## 2. Reading input

```cpp
int n, m; cin >> n >> m;

vector<int> a(n);
for (auto& x : a) cin >> x;

vector<vector<int>> g(n, vector<int>(m));
for (auto& row : g) for (auto& x : row) cin >> x;

string s; cin >> s;                     // one token
string line; getline(cin, line);        // whole line
cin >> n; cin.ignore(); getline(cin, line);   // ignore() eats the newline

while (cin >> x) { }                    // until EOF
```

---

## 3. Sorting and comparators — three ways

```cpp
sort(all(a));                                        // ascending
sort(all(a), greater<int>());                        // descending

// 1. lambda
sort(all(v), [](const pii& x, const pii& y) {
    if (x.second != y.second) return x.second < y.second;   // by second asc
    return x.first > y.first;                               // tie: first desc
});

// 2. member operator<
struct Edge { int u, v, w;
    bool operator<(const Edge& o) const { return w < o.w; } };
sort(all(edges));

// 3. free comparator function
bool cmp(const Edge& a, const Edge& b) { return a.w < b.w; }
sort(all(edges), cmp);
```

**Sort by index using values:**
```cpp
vector<int> idx(n); iota(all(idx), 0);
sort(all(idx), [&](int i, int j) { return a[i] < a[j]; });
```

---

## 4. Binary search

```cpp
int lo = lower_bound(all(a), x) - a.begin();      // first index with a[i] >= x
int hi = upper_bound(all(a), x) - a.begin();      // first index with a[i] >  x
int cnt = hi - lo;                                 // occurrences of x
bool found = binary_search(all(a), x);

// binary search on the answer
int L = 1, R = 1e9;
while (L < R) {
    int mid = L + (R - L) / 2;                     // overflow-safe
    if (feasible(mid)) R = mid;
    else               L = mid + 1;
}
// answer = L
```

---

## 5. Graphs

```cpp
vector<vector<int>> g(n);
for (int i = 0; i < m; i++) {
    int u, v; cin >> u >> v; u--; v--;             // to 0-indexed
    g[u].push_back(v);
    g[v].push_back(u);                             // undirected only
}

// BFS
vector<int> dist(n, -1);
queue<int> q; q.push(src); dist[src] = 0;
while (!q.empty()) {
    int u = q.front(); q.pop();
    for (int v : g[u]) if (dist[v] == -1) { dist[v] = dist[u] + 1; q.push(v); }
}

// Dijkstra
vector<vector<pii>> adj(n);                        // {to, weight}
vector<ll> d(n, LINF); d[src] = 0;
priority_queue<pair<ll,int>, vector<pair<ll,int>>, greater<>> pq;
pq.push({0, src});
while (!pq.empty()) {
    auto [cd, u] = pq.top(); pq.pop();
    if (cd > d[u]) continue;                       // stale entry
    for (auto [v, w] : adj[u])
        if (cd + w < d[v]) { d[v] = cd + w; pq.push({d[v], v}); }
}
```

---

## 6. Union-Find

```cpp
struct DSU {
    vector<int> p, sz;
    int comps;
    DSU(int n) : p(n), sz(n, 1), comps(n) { iota(all(p), 0); }
    int find(int x) { while (p[x] != x) { p[x] = p[p[x]]; x = p[x]; } return x; }
    bool unite(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return false;
        if (sz[a] < sz[b]) swap(a, b);
        p[b] = a; sz[a] += sz[b]; comps--;
        return true;
    }
};
```

---

## 7. Modular arithmetic

```cpp
const int MOD = 1e9 + 7;

ll power(ll b, ll e, ll m = MOD) {
    ll r = 1; b %= m;
    while (e > 0) { if (e & 1) r = r * b % m; b = b * b % m; e >>= 1; }
    return r;
}
ll inv(ll x) { return power(x, MOD - 2); }         // MOD must be prime

const int N = 200005;
ll fact[N], inv_fact[N];
void init() {
    fact[0] = 1;
    for (int i = 1; i < N; i++) fact[i] = fact[i-1] * i % MOD;
    inv_fact[N-1] = inv(fact[N-1]);
    for (int i = N-1; i > 0; i--) inv_fact[i-1] = inv_fact[i] * i % MOD;
}
ll nCr(int n, int r) {
    if (r < 0 || r > n) return 0;
    return fact[n] * inv_fact[r] % MOD * inv_fact[n-r] % MOD;
}
```

---

## 8. Grid traversal

```cpp
const int dr[] = {1, -1, 0, 0};
const int dc[] = {0, 0, 1, -1};
// 8-directional: {1,-1,0,0,1,1,-1,-1} / {0,0,1,-1,1,-1,1,-1}

for (int k = 0; k < 4; k++) {
    int nr = r + dr[k], nc = c + dc[k];
    if (nr < 0 || nr >= R || nc < 0 || nc >= C) continue;
    // ...
}
```

---

## 9. String helpers

```cpp
// split on a delimiter
vector<string> split(const string& s, char d) {
    vector<string> out; string cur;
    stringstream ss(s);
    while (getline(ss, cur, d)) out.push_back(cur);
    return out;
}

// join
string join(const vector<string>& v, const string& sep) {
    string r;
    for (int i = 0; i < (int)v.size(); i++) { if (i) r += sep; r += v[i]; }
    return r;
}

// frequency of lowercase letters
int cnt[26] = {};
for (char c : s) cnt[c - 'a']++;

transform(all(s), s.begin(), ::tolower);
```

---

## 10. Debugging

```cpp
#define dbg(x) cerr << #x << " = " << (x) << '\n'

template<typename T>
void print(const vector<T>& v) {
    for (auto& x : v) cerr << x << ' ';
    cerr << '\n';
}
```

Write debug output to `cerr`, not `cout` — judges compare `cout` only, so `cerr` cannot corrupt your submission.

---

## 11. Output formatting

```cpp
cout << fixed << setprecision(6) << ans << '\n';    // <iomanip>
cout << (ok ? "YES" : "NO") << '\n';
for (int x : a) cout << x << " \n"[&x == &a.back()];  // space, newline at end
```

---

## 12. Pre-submit checklist

- [ ] `long long` wherever a product or a large sum appears
- [ ] `ios_base::sync_with_stdio(false); cin.tie(nullptr);` present
- [ ] No `endl` inside a loop
- [ ] `mid = lo + (hi - lo) / 2`, never `(lo + hi) / 2`
- [ ] Vectors passed by reference, not by value
- [ ] `.empty()` checked before `top()` / `front()` / `back()`
- [ ] Comparator returns **strict** `<`, never `<=`
- [ ] Arrays sized `n+1` if you index 1-based
- [ ] `accumulate(all(a), 0LL)` not `0`
