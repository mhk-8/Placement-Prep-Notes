
# Java Collections and Core Java — One Pager

> **Use:** Oracle, Amazon, Flipkart/Walmart machine-coding, and any OA where Java is the only
> allowed language. ⚠️ Java is your weakest language — this sheet is the minimum to not be
> filtered out by a Java-only round. See `../08_Company_Wise/Oracle/00-process-and-oa.md` for
> the go/no-go decision.

---

## 1. The boilerplate ⭐

```java
import java.util.*;
import java.io.*;

public class Main {
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));   // ⭐ fast
        StringBuilder sb = new StringBuilder();                                    // ⭐ never += in a loop
        int n = Integer.parseInt(br.readLine().trim());
        StringTokenizer st = new StringTokenizer(br.readLine());
        int[] a = new int[n];
        for (int i = 0; i < n; i++) a[i] = Integer.parseInt(st.nextToken());
        sb.append(ans).append('\n');
        System.out.print(sb);
    }
}
```
> ⚠️ `Scanner` is 5-10x slower than `BufferedReader`. In a tight OA it is the difference
> between passing and TLE.

---

## 2. The collection hierarchy

```
Iterable
└── Collection
    ├── List      → ArrayList, LinkedList, Vector (legacy, synchronised)
    ├── Set       → HashSet, LinkedHashSet, TreeSet (SortedSet/NavigableSet)
    └── Queue     → ArrayDeque, LinkedList, PriorityQueue
                    └── Deque → ArrayDeque, LinkedList

Map (NOT a Collection ⭐ a common trick question)
    → HashMap, LinkedHashMap, TreeMap, Hashtable (legacy), ConcurrentHashMap
```

---

## 3. Implementation choice table ⭐⭐

| Interface | Implementation | Backing | Order | get/contains | add | Notes |
|---|---|---|---|---|---|---|
| List | **ArrayList** | array | index | O(1) | O(1) am. | default ⭐ |
| List | LinkedList | doubly linked | index | O(n) | O(1) at ends | also a Deque |
| Set | **HashSet** | HashMap | none | O(1) | O(1) | |
| Set | LinkedHashSet | HashMap + list | insertion | O(1) | O(1) | |
| Set | **TreeSet** | red-black tree | **sorted** | O(log n) | O(log n) | `floor/ceiling/higher/lower` ⭐ |
| Map | **HashMap** | array of buckets | none | O(1) | O(1) | null key allowed (one) |
| Map | LinkedHashMap | + linked list | insertion / **access** | O(1) | O(1) | ⭐ LRU in 5 lines |
| Map | **TreeMap** | red-black tree | sorted by key | O(log n) | O(log n) | `firstKey`, `subMap`, `headMap` |
| Map | Hashtable | synchronised | none | O(1) | O(1) | legacy, no nulls |
| Map | ConcurrentHashMap | segmented/CAS | none | O(1) | O(1) | thread-safe, no null key |
| Queue | **ArrayDeque** | circular array | FIFO/LIFO | — | O(1) | ⭐ use instead of Stack |
| Queue | **PriorityQueue** | binary heap | **min-heap** | O(1) peek | O(log n) | ⚠️ iteration is unordered |
| — | Stack | Vector | LIFO | — | O(1) | legacy, synchronised — avoid ⚠️ |

### HashMap internals ⭐⭐ (asked constantly at Oracle/Amazon)
```
Array of buckets. index = (n - 1) & hash(key), where hash(k) = h ^ (h >>> 16)  [spreading]
Collision → singly linked list in the bucket.
Java 8+: a bucket with ≥ 8 entries (TREEIFY_THRESHOLD) and table size ≥ 64 converts to a
         red-black tree → worst case O(log n) instead of O(n). ⭐
Default capacity 16, load factor 0.75 → resize (double + rehash) at 12 entries.
Requires: equals() and hashCode() contract — equal objects MUST have equal hash codes. ⚠️
Mutating a key after insertion makes the entry unreachable. Keys should be immutable.
```

### The LRU cache in five lines ⭐
```java
new LinkedHashMap<K,V>(cap, 0.75f, true) {          // true = access order
    protected boolean removeEldestEntry(Map.Entry<K,V> e) { return size() > cap; }
};
```

---

## 4. Idioms you need in a timed round ⭐

```java
// min-heap / max-heap
PriorityQueue<Integer> min = new PriorityQueue<>();
PriorityQueue<Integer> max = new PriorityQueue<>(Comparator.reverseOrder());
PriorityQueue<int[]> pq = new PriorityQueue<>((x, y) -> x[0] - y[0]);   // ⚠️ overflow-prone;
PriorityQueue<int[]> ok = new PriorityQueue<>(Comparator.comparingInt(x -> x[0]));  // prefer this

// sorting
Arrays.sort(a);                                      // int[]  : dual-pivot quicksort, NOT stable
Arrays.sort(objArr);                                 // Object[]: Timsort, STABLE  ⭐ the asymmetry
Collections.sort(list);
list.sort(Comparator.comparingInt(P::getA).thenComparing(P::getB).reversed());
Arrays.sort(iv, (x, y) -> Integer.compare(x[0], y[0]));

// map idioms
map.getOrDefault(k, 0)
map.put(k, map.getOrDefault(k, 0) + 1);
map.merge(k, 1, Integer::sum);                       // ⭐ cleaner frequency count
map.computeIfAbsent(u, x -> new ArrayList<>()).add(v);   // ⭐ adjacency list
for (Map.Entry<K,V> e : map.entrySet()) { e.getKey(); e.getValue(); }

// conversions
int[] arr  = list.stream().mapToInt(Integer::intValue).toArray();
List<Integer> l = Arrays.stream(arr).boxed().collect(Collectors.toList());
Integer[] boxed = list.toArray(new Integer[0]);
List<Integer> fixed = Arrays.asList(1,2,3);          // ⚠️ fixed-size; add() throws
new ArrayList<>(Arrays.asList(1,2,3));               // mutable

// 2-D
int[][] dp = new int[n][m];                          // zero-filled
Arrays.fill(row, -1);  for (int[] r : dp) Arrays.fill(r, -1);
int[][] copy = Arrays.stream(dp).map(int[]::clone).toArray(int[][]::new);

// strings
new StringBuilder(s).reverse().toString()
s.toCharArray();  String.valueOf(arr);  String.join(",", list)
s.split("\\s+")                                      // ⚠️ regex, not a literal
s.chars().filter(c -> c=='a').count()
// ⚠️ String is immutable; s += c in a loop is O(n²). StringBuilder always.
```

### Binary search in the library
```java
Arrays.binarySearch(a, key)         // returns -(insertionPoint) - 1 if absent ⚠️ not -1
Collections.binarySearch(list, key)
TreeMap: floorKey, ceilingKey, higherKey, lowerKey, firstKey, lastKey, headMap, tailMap, subMap
TreeSet: floor, ceiling, higher, lower, first, last, pollFirst, pollLast      ⭐ your lower_bound
```

---

## 5. Core Java interview answers ⭐⭐

| Question | Answer |
|---|---|
| `==` vs `.equals()` | Reference identity vs value equality. ⚠️ `new String("a") == "a"` is false |
| String pool | Literals are interned in the heap's string pool; `intern()` forces it |
| Why is String immutable | Security, hashcode caching, safe sharing in the pool, thread safety |
| `equals`/`hashCode` contract | Equal → same hash. Unequal may collide. Override both or neither ⚠️ |
| Overloading vs overriding | Compile-time, same name different params vs run-time, same signature |
| Abstract class vs interface | State + partial implementation, single inheritance vs multiple, `default` methods since 8 |
| `final` / `finally` / `finalize` | Immutable binding / always-run block / deprecated GC hook |
| Checked vs unchecked exception | Must be declared/caught vs `RuntimeException` subclasses |
| try-with-resources | Auto-closes `AutoCloseable`; suppresses correctly |
| Pass by value | **Java is always pass-by-value** — object *references* are passed by value ⭐ |
| Autoboxing | `int ↔ Integer`. ⚠️ Integer cache is −128..127, so `==` on larger values fails |
| Generics / type erasure | Compile-time only; no `new T[]`, no primitives, unchecked warnings |
| `static` | Per-class, not per-instance; `static` blocks run at class load |
| Access modifiers | `private` < default (package) < `protected` < `public` |
| JVM / JRE / JDK | Runtime engine / runtime + libs / + compiler and tools |
| Class loading | Bootstrap → Platform → Application; lazy, with verification |
| Memory areas | Heap (young: eden+survivors, old) · stack per thread · metaspace · code cache |
| GC | Generational; minor vs major; G1 is the modern default. `System.gc()` is a hint ⚠️ |
| Memory leak in Java | Reachable-but-unused objects: static collections, unclosed resources, listeners |
| `synchronized` | Monitor lock on an object; mutual exclusion + happens-before visibility |
| `volatile` | Visibility and ordering, **not** atomicity ⚠️ `v++` is still racy |
| `Runnable` vs `Callable` | No return / returns a value and may throw, used with `Future` |
| Thread pool | `ExecutorService`; avoids per-task thread creation cost |
| Fail-fast iterator | Throws `ConcurrentModificationException`; use `Iterator.remove()` or a concurrent collection |
| `Comparable` vs `Comparator` | Natural order inside the class vs external, multiple orderings ⭐ |
| Immutable class recipe | `final` class, `private final` fields, no setters, deep-copy mutable fields in/out |
| Singleton | Enum, or static holder idiom; double-checked locking needs `volatile` ⚠️ |
| SOLID | See `oop-one-pager.md` |

### Output-prediction traps ⚠️
```java
Integer a = 127, b = 127;   a == b          // true  (Integer cache)
Integer c = 128, d = 128;   c == d          // false ⚠️ outside the cache
System.out.println(0.1 + 0.2);              // 0.30000000000000004
System.out.println('a' + 1);                // 98 — int arithmetic, not "a1"
System.out.println("" + 'a' + 1);           // a1
int[] x = new int[3];                       // {0,0,0}; String[] gives {null,null,null}
System.out.println(10/3 + " " + 10%3);      // 3 1   ;  -10/3 == -3, -10%3 == -1
long big = 1000000 * 1000000;               // ⚠️ overflows in int BEFORE widening. Use 1000000L
try { return 1; } finally { return 2; }     // returns 2 ⚠️
String s = null; System.out.println(s);     // prints "null", no NPE (println(Object) overload)
```

---

## 6. Streams minimum (Oracle/enterprise rounds)

```java
list.stream().filter(x -> x > 0).map(x -> x * 2).sorted().collect(Collectors.toList());
IntStream.range(0, n).boxed().collect(Collectors.toList());
list.stream().mapToInt(Integer::intValue).sum();
list.stream().collect(Collectors.groupingBy(P::getType, Collectors.counting()));
list.stream().anyMatch(p) · allMatch · findFirst() · max(Comparator...)
Optional<T>: isPresent(), orElse(x), map(), ifPresent()
// ⚠️ streams are single-use and lazy until a terminal operation; slower than loops in hot paths
```

---

## Recall questions
1. Why is `Arrays.sort(int[])` not stable while `Arrays.sort(Integer[])` is?
2. Explain what changed in Java 8's `HashMap` and why.
3. `Integer a = 128, b = 128; a == b` — what prints and why?
4. Is Java pass-by-value or pass-by-reference? Justify with an example.
5. `volatile` vs `synchronized` — what does each guarantee?
6. Write a bounded LRU cache using only the standard library.
7. What does `Arrays.binarySearch` return when the key is absent?
