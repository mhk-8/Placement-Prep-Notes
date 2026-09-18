# Java — Syntax Reference

> Java is verbose, so fluency here is mostly about typing the boilerplate without thinking. Its compensation is that the compiler catches a large class of errors Python lets through silently.

---

## 1. The starter file

```java
import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    static StringBuilder sb = new StringBuilder();

    public static void main(String[] args) throws IOException {
        int t = 1;
        // t = Integer.parseInt(br.readLine().trim());
        while (t-- > 0) solve();
        System.out.print(sb);          // ONE write at the end
    }

    static void solve() throws IOException {
        int n = Integer.parseInt(br.readLine().trim());
        StringTokenizer st = new StringTokenizer(br.readLine());
        int[] a = new int[n];
        for (int i = 0; i < n; i++) a[i] = Integer.parseInt(st.nextToken());
        sb.append(ans).append('\n');
    }
}
```

**`Scanner` is far too slow for competitive input** — it uses regular expressions internally. `BufferedReader` with `StringTokenizer` is several times faster, and batching output into one `StringBuilder` avoids a flush per line. On a 10⁶-token input this is the difference between passing and a TLE.

The class must be named `Main` on almost every judge.

---

## 2. Primitives and wrappers

| Primitive | Size | Range | Wrapper |
|---|---|---|---|
| `byte` | 1 B | −128 … 127 | `Byte` |
| `short` | 2 B | ±32,767 | `Short` |
| `int` | 4 B | ±2.1 × 10⁹ | `Integer` |
| `long` | 8 B | ±9.2 × 10¹⁸ | `Long` |
| `float` | 4 B | ~7 digits | `Float` |
| `double` | 8 B | ~15 digits | `Double` |
| `char` | 2 B | UTF-16 code unit | `Character` |
| `boolean` | — | true/false | `Boolean` |

```java
Integer.MAX_VALUE, Long.MAX_VALUE, Double.MAX_VALUE
long big = 1_000_000_000L;              // L suffix is REQUIRED past int range
int x = (int) longValue;                // explicit narrowing cast
```

**Autoboxing** converts between `int` and `Integer` automatically — convenient, and the source of the `==` trap in `gotchas.md`. Generic collections can only hold wrappers, never primitives, which is why `List<int>` does not compile.

---

## 3. Arrays

```java
int[] a = new int[n];                   // zero-initialised
int[] b = {1, 2, 3};
int[][] g = new int[n][m];
int[][] jag = new int[n][];             // rows allocated separately

a.length                                // a FIELD, no parentheses
Arrays.fill(a, -1);
Arrays.sort(a);                         // primitives: dual-pivot quicksort
Arrays.sort(objs, comparator);          // objects: Timsort, STABLE
Arrays.copyOf(a, newLen);
Arrays.copyOfRange(a, from, to);        // to is EXCLUSIVE
Arrays.toString(a);                     // 1-D printing
Arrays.deepToString(g);                 // 2-D printing
Arrays.binarySearch(a, key);            // requires a sorted array
Arrays.equals(a, b);                    // == compares references!
Arrays.stream(a).sum();
```

`a.length` for arrays, `s.length()` for strings, `list.size()` for collections — three different spellings of the same idea, and a routine source of compile errors.

---

## 4. Strings

```java
String s = "hello";
s.length(); s.charAt(i); s.substring(a, b);   // b is EXCLUSIVE
s.indexOf("lo");                              // -1 if absent
s.contains("ell"); s.startsWith("he");
s.split(",");                                 // takes a REGEX
s.trim(); s.strip();
s.toCharArray();
String.valueOf(x); Integer.parseInt(s);
String.join(",", list);
s.equals(t);                                  // ALWAYS use equals, never ==
s.compareTo(t);
s.replace('a','b'); s.toUpperCase();
String.format("%.2f", d);
```

**Strings are immutable.** `s += c` in a loop is O(n²); use `StringBuilder`:

```java
StringBuilder sb = new StringBuilder();
sb.append(x).append(' ');
sb.insert(0, "pre"); sb.reverse(); sb.setCharAt(i, c);
sb.deleteCharAt(i);
String result = sb.toString();
```

`substring(a, b)` takes an **exclusive** end index — the opposite convention from C++'s `substr(pos, len)`.

---

## 5. Collections

```java
List<Integer> list = new ArrayList<>();
list.add(x); list.get(i); list.set(i, x);
list.remove(i);                     // by INDEX for int
list.remove(Integer.valueOf(x));    // by VALUE
list.contains(x); list.size(); list.isEmpty();
Collections.sort(list);
Collections.reverse(list);
Collections.max(list); Collections.min(list);

Map<String,Integer> map = new HashMap<>();
map.put(k, v); map.get(k);          // null if absent
map.getOrDefault(k, 0);
map.merge(k, 1, Integer::sum);      // the idiomatic counter increment
map.computeIfAbsent(k, z -> new ArrayList<>()).add(x);
map.containsKey(k); map.remove(k);
for (Map.Entry<String,Integer> e : map.entrySet()) { e.getKey(); e.getValue(); }

Set<Integer> set = new HashSet<>();
TreeMap<Integer,Integer> tm = new TreeMap<>();   // ORDERED, O(log n)
tm.firstKey(); tm.lastKey();
tm.floorKey(x); tm.ceilingKey(x);                // <= x  /  >= x
tm.higherKey(x); tm.lowerKey(x);                 // strict
tm.headMap(x); tm.tailMap(x); tm.subMap(a, b);

Deque<Integer> dq = new ArrayDeque<>();          // use as both stack and queue
dq.push(x); dq.pop();                            // stack (front)
dq.offer(x); dq.poll();                          // queue
dq.peekFirst(); dq.peekLast();

PriorityQueue<Integer> pq = new PriorityQueue<>();                      // MIN-heap
PriorityQueue<Integer> maxPq = new PriorityQueue<>(Collections.reverseOrder());
```

**`TreeMap` is Java's biggest advantage over Python** for contest work — an ordered map with `floorKey`/`ceilingKey` in O(log n), which Python's standard library simply does not have.

---

## 6. Comparators

```java
Arrays.sort(arr, (x, y) -> x[1] - y[1]);              // risky: can overflow
Arrays.sort(arr, (x, y) -> Integer.compare(x[1], y[1]));   // always safe

list.sort(Comparator.comparingInt(P::getAge));
list.sort(Comparator.comparing(P::getName).thenComparing(P::getAge));
list.sort(Comparator.comparingInt(P::getAge).reversed());

class P implements Comparable<P> {
    public int compareTo(P o) { return Integer.compare(this.v, o.v); }
}
```

**Never write `a - b` in a comparator.** With `a = 2_000_000_000` and `b = -2_000_000_000` the subtraction overflows and returns the wrong sign. `Integer.compare(a, b)` is correct and equally fast.

---

## 7. Control flow and syntax

```java
for (int i = 0; i < n; i++) { }
for (int x : a) { }                       // read-only for primitives
while (cond) { }
do { } while (cond);

switch (x) { case 1: ...; break; default: ...; }
String r = switch (x) { case 1 -> "one"; default -> "other"; };   // Java 14+

if (a instanceof String s) { }            // pattern matching, Java 16+
var list = new ArrayList<Integer>();      // local type inference, Java 10+

try { } catch (IOException e) { } finally { }
try (BufferedReader r = ...) { }          // try-with-resources, auto-closes
```

---

## 8. Streams (concise, but slower than loops)

```java
Arrays.stream(a).sum();
Arrays.stream(a).max().getAsInt();
list.stream().filter(x -> x > 0).map(x -> x * 2).collect(Collectors.toList());
list.stream().mapToInt(Integer::intValue).sum();
IntStream.range(0, n).forEach(i -> { });
list.stream().collect(Collectors.groupingBy(P::getDept));
```

Streams are excellent for readability and noticeably slower than a plain loop. Use them for clarity in interview code; use loops in a tight OA.

---

## 9. Classes

```java
class Node implements Comparable<Node> {
    int val; Node next;
    Node(int val) { this.val = val; }

    @Override public int compareTo(Node o) { return Integer.compare(val, o.val); }
    @Override public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Node)) return false;
        return val == ((Node) o).val;
    }
    @Override public int hashCode() { return Integer.hashCode(val); }
    @Override public String toString() { return "Node(" + val + ")"; }
}
```

**Override `equals` and `hashCode` together, always.** Overriding one alone breaks every hash-based collection silently.

---

## 10. Output

```java
System.out.println(x);                    // flushes — slow in a loop
sb.append(x).append('\n');                // batch, then print once
System.out.printf("%.6f%n", d);
System.out.print(sb);
```
