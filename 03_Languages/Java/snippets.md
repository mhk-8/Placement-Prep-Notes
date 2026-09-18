# Java — Snippets

> Copy-ready blocks. The boilerplate is long, so type it from memory until it costs you no thought.

---

## 1. Starter template

```java
import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static StringBuilder sb = new StringBuilder();
    static StringTokenizer st;

    static int ni() throws IOException { return Integer.parseInt(next()); }
    static long nl() throws IOException { return Long.parseLong(next()); }
    static String next() throws IOException {
        while (st == null || !st.hasMoreTokens())
            st = new StringTokenizer(br.readLine());
        return st.nextToken();
    }

    public static void main(String[] args) throws IOException {
        int t = 1;
        // t = ni();
        while (t-- > 0) solve();
        System.out.print(sb);
    }

    static void solve() throws IOException {
        int n = ni();
        int[] a = new int[n];
        for (int i = 0; i < n; i++) a[i] = ni();
        sb.append(ans).append('\n');
    }
}
```

The `next()` helper handles tokens split across lines, so you never have to think about line boundaries again.

---

## 2. Reading input

```java
int n = Integer.parseInt(br.readLine().trim());

StringTokenizer st = new StringTokenizer(br.readLine());
int a = Integer.parseInt(st.nextToken());
int b = Integer.parseInt(st.nextToken());

int[] arr = Arrays.stream(br.readLine().split(" "))
                  .mapToInt(Integer::parseInt).toArray();

char[][] grid = new char[n][];
for (int i = 0; i < n; i++) grid[i] = br.readLine().trim().toCharArray();

String line;
while ((line = br.readLine()) != null) { }     // until EOF
```

---

## 3. Sorting and comparators

```java
Arrays.sort(a);                                   // primitives, ascending
Integer[] boxed = ...; Arrays.sort(boxed, Collections.reverseOrder());

// 2-D array by column 1, then column 0 descending
Arrays.sort(intervals, (x, y) ->
    x[1] != y[1] ? Integer.compare(x[1], y[1]) : Integer.compare(y[0], x[0]));

list.sort(Comparator.comparingInt(P::getAge));
list.sort(Comparator.comparing(P::getName).thenComparingInt(P::getAge));
list.sort(Comparator.comparingInt(P::getAge).reversed());

// descending primitives: box, or sort ascending and reverse
Arrays.sort(a);
for (int i = 0, j = a.length - 1; i < j; i++, j--) { int t = a[i]; a[i] = a[j]; a[j] = t; }
```

**Always `Integer.compare(x, y)`, never `x - y`** — the subtraction overflows for large-magnitude values and returns the wrong sign.

---

## 4. Binary search

```java
int idx = Arrays.binarySearch(a, key);            // negative if absent:
// insertion point = -(idx) - 1

// lower_bound by hand
static int lowerBound(int[] a, int x) {
    int lo = 0, hi = a.length;
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] >= x) hi = mid; else lo = mid + 1;
    }
    return lo;
}

// binary search on the answer
long lo = 1, hi = (long) 1e18;
while (lo < hi) {
    long mid = lo + (hi - lo) / 2;
    if (feasible(mid)) hi = mid; else lo = mid + 1;
}
```

`TreeSet` and `TreeMap` give the same thing out of the box: `ceiling`, `floor`, `higher`, `lower`.

---

## 5. Maps and counting

```java
Map<Integer,Integer> cnt = new HashMap<>();
for (int x : a) cnt.merge(x, 1, Integer::sum);

Map<String,List<String>> groups = new HashMap<>();
for (String w : words) {
    char[] c = w.toCharArray(); Arrays.sort(c);
    groups.computeIfAbsent(new String(c), k -> new ArrayList<>()).add(w);
}

int[] freq = new int[26];
for (char c : s.toCharArray()) freq[c - 'a']++;     // faster than a HashMap
```

---

## 6. Heaps

```java
PriorityQueue<Integer> minPq = new PriorityQueue<>();
PriorityQueue<Integer> maxPq = new PriorityQueue<>(Collections.reverseOrder());
PriorityQueue<int[]> pq = new PriorityQueue<>((x, y) -> Integer.compare(x[0], y[0]));

pq.offer(x); pq.poll(); pq.peek(); pq.size();

// k-th largest with a bounded min-heap
for (int x : a) { minPq.offer(x); if (minPq.size() > k) minPq.poll(); }
int kth = minPq.peek();

// heapify a collection in O(n)
PriorityQueue<Integer> h = new PriorityQueue<>(list);
```

---

## 7. Graphs

```java
List<List<Integer>> g = new ArrayList<>();
for (int i = 0; i < n; i++) g.add(new ArrayList<>());
for (int i = 0; i < m; i++) {
    int u = ni() - 1, v = ni() - 1;
    g.get(u).add(v);
    g.get(v).add(u);
}

// BFS
int[] dist = new int[n];
Arrays.fill(dist, -1);
Deque<Integer> q = new ArrayDeque<>();
q.offer(src); dist[src] = 0;
while (!q.isEmpty()) {
    int u = q.poll();
    for (int v : g.get(u))
        if (dist[v] == -1) { dist[v] = dist[u] + 1; q.offer(v); }
}

// Dijkstra
long[] d = new long[n];
Arrays.fill(d, Long.MAX_VALUE / 4);
d[src] = 0;
PriorityQueue<long[]> pq = new PriorityQueue<>((x, y) -> Long.compare(x[0], y[0]));
pq.offer(new long[]{0, src});
while (!pq.isEmpty()) {
    long[] cur = pq.poll();
    int u = (int) cur[1];
    if (cur[0] > d[u]) continue;                 // stale entry
    for (int[] e : adj.get(u)) {
        if (cur[0] + e[1] < d[e[0]]) {
            d[e[0]] = cur[0] + e[1];
            pq.offer(new long[]{d[e[0]], e[0]});
        }
    }
}
```

Using `Long.MAX_VALUE / 4` rather than `Long.MAX_VALUE` as infinity means `INF + weight` does not overflow during relaxation.

---

## 8. Union-Find

```java
static int[] par, sz;
static void init(int n) {
    par = new int[n]; sz = new int[n];
    for (int i = 0; i < n; i++) { par[i] = i; sz[i] = 1; }
}
static int find(int x) {
    while (par[x] != x) { par[x] = par[par[x]]; x = par[x]; }
    return x;
}
static boolean union(int a, int b) {
    a = find(a); b = find(b);
    if (a == b) return false;
    if (sz[a] < sz[b]) { int t = a; a = b; b = t; }
    par[b] = a; sz[a] += sz[b];
    return true;
}
```

---

## 9. LRU cache with LinkedHashMap

```java
class LRUCache extends LinkedHashMap<Integer,Integer> {
    private final int cap;
    LRUCache(int cap) {
        super(cap, 0.75f, true);          // true = ACCESS order
        this.cap = cap;
    }
    @Override protected boolean removeEldestEntry(Map.Entry<Integer,Integer> e) {
        return size() > cap;
    }
}
```

Access-order `LinkedHashMap` plus `removeEldestEntry` gives a complete LRU cache in six lines — a genuinely good thing to know for an interview, alongside the hand-rolled hash-map-plus-doubly-linked-list version.

---

## 10. Modular arithmetic

```java
static final int MOD = 1_000_000_007;

static long power(long b, long e, long m) {
    long r = 1; b %= m;
    while (e > 0) { if ((e & 1) == 1) r = r * b % m; b = b * b % m; e >>= 1; }
    return r;
}
static long inv(long x) { return power(x, MOD - 2, MOD); }
```

---

## 11. Strings

```java
StringBuilder sb = new StringBuilder();
for (...) sb.append(c);
String result = sb.toString();

new StringBuilder(s).reverse().toString();
String.join(",", list);
s.split("\\s+");                    // one or more whitespace (regex!)
s.chars().filter(c -> c == 'a').count();
Character.isDigit(c); Character.isLetter(c); Character.toLowerCase(c);
```

---

## 12. Pre-submit checklist

- [ ] `BufferedReader`, not `Scanner`
- [ ] Output batched into a `StringBuilder`, printed once
- [ ] `long` wherever a product or a large sum appears; `1_000_000_000L` with the `L`
- [ ] Comparators use `Integer.compare`, never `a - b`
- [ ] `list.remove(Integer.valueOf(x))` when removing by value
- [ ] Strings compared with `.equals`, never `==`
- [ ] `StringBuilder` for any loop that builds a string
- [ ] `split` arguments escaped as regexes
- [ ] Infinity chosen so that `INF + w` does not overflow
- [ ] `equals` and `hashCode` overridden together
