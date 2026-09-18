# Java — Collections and Complexities

> The lookup table. Java's collection framework is larger than C++'s or Python's, and choosing well is most of the performance story.

---

## 1. List

| Implementation | get(i) | add(end) | add/remove(middle) | contains | Backing |
|---|---|---|---|---|---|
| `ArrayList` | **O(1)** | O(1) amortised | O(n) | O(n) | array |
| `LinkedList` | O(n) | O(1) | O(1) **given a node** | O(n) | doubly linked |

**Use `ArrayList` by default.** `LinkedList`'s theoretical insert advantage is almost always destroyed by cache behaviour and by the O(n) walk needed to reach the position.

```java
list.add(x); list.add(i, x); list.get(i); list.set(i, x);
list.remove(i);                       // by INDEX
list.remove(Integer.valueOf(x));      // by VALUE — the classic trap
list.subList(a, b);                   // a VIEW, not a copy
```

---

## 2. Map

| Implementation | get/put | Ordered? | Notes |
|---|---|---|---|
| `HashMap` | **O(1) avg**, O(log n) worst | no | buckets become red-black trees past ~8 collisions |
| `LinkedHashMap` | O(1) avg | insertion (or access) order | access-order mode makes an LRU cache trivial |
| `TreeMap` | **O(log n)** | sorted by key | `floorKey`, `ceilingKey`, `subMap` |

```java
map.getOrDefault(k, 0);
map.merge(k, 1, Integer::sum);                       // counter increment
map.computeIfAbsent(k, z -> new ArrayList<>()).add(x);   // grouping
map.putIfAbsent(k, v);
map.forEach((k, v) -> ...);
```

**`TreeMap` navigation methods** are the reason to reach for Java on certain problems:
```java
tm.floorKey(x)     // greatest key <= x
tm.ceilingKey(x)   // smallest key >= x
tm.lowerKey(x)     // greatest key <  x
tm.higherKey(x)    // smallest key >  x
tm.firstEntry(); tm.lastEntry(); tm.pollFirstEntry();
tm.headMap(x); tm.tailMap(x); tm.subMap(a, true, b, false);
```

---

## 3. Set

| Implementation | add/contains | Ordered? |
|---|---|---|
| `HashSet` | O(1) avg | no |
| `LinkedHashSet` | O(1) avg | insertion |
| `TreeSet` | O(log n) | sorted, with the same navigation methods as `TreeMap` |

---

## 4. Queue, Deque and PriorityQueue

| Type | Operations | Complexity |
|---|---|---|
| `ArrayDeque` | `push`/`pop`/`offer`/`poll`/`peek` | **O(1)** at both ends |
| `LinkedList` (as Deque) | same | O(1), but slower constants |
| `PriorityQueue` | `offer`/`poll` | O(log n); `peek` O(1) |
| `PriorityQueue(collection)` | heapify | **O(n)** |

```java
Deque<Integer> stack = new ArrayDeque<>();     // prefer over the legacy Stack class
stack.push(x); stack.pop(); stack.peek();

Deque<Integer> queue = new ArrayDeque<>();
queue.offer(x); queue.poll(); queue.peek();

PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> Integer.compare(a[0], b[0]));
```

**`PriorityQueue` is a MIN-heap by default** — the opposite of C++. For a max-heap pass `Collections.reverseOrder()` or a reversed comparator.

**`PriorityQueue.remove(Object)` is O(n)**, because a heap has no ordering to search by. Use lazy deletion instead.

Avoid the legacy `Stack` and `Vector`: they are synchronised and therefore slower, and `Stack` iterates in the wrong order.

---

## 5. Arrays and Collections utilities

| Call | Complexity | Notes |
|---|---|---|
| `Arrays.sort(int[])` | O(n log n) avg, **O(n²) worst** | dual-pivot quicksort, **not stable** |
| `Arrays.sort(T[])` | O(n log n) | Timsort, **stable** |
| `Arrays.sort(T[], cmp)` | O(n log n) | Timsort, stable |
| `Arrays.binarySearch` | O(log n) | sorted input required |
| `Arrays.fill` | O(n) | |
| `Arrays.copyOf` / `copyOfRange` | O(n) | end index exclusive |
| `Collections.sort(list)` | O(n log n) | Timsort, stable |
| `Collections.reverse/shuffle` | O(n) | |
| `Collections.binarySearch` | O(log n) for a RandomAccess list | O(n) for a `LinkedList` |

**The stability asymmetry is a favourite MCQ:** primitive arrays are sorted unstably (there are no distinguishable equal primitives, so it does not matter), object arrays and lists stably.

**The O(n²) worst case on primitives is real** — adversarial inputs against dual-pivot quicksort exist. If you are worried, box to `Integer[]` (Timsort, guaranteed O(n log n)) or shuffle first.

---

## 6. Strings

| Operation | Complexity |
|---|---|
| `charAt` | O(1) |
| `length()` | O(1) |
| `substring(a, b)` | O(b − a) — copies since Java 7 |
| `s + t` | O(n + m) |
| **`s += c` in a loop** | **O(n²)** |
| `StringBuilder.append` | O(1) amortised |
| `equals` | O(n) |
| `indexOf` | O(n·m) worst |
| `split` | O(n), compiles a regex |

`s.split(",")` takes a **regular expression**, so `split(".")` splits on every character and `split("\\.")` is what you meant.

---

## 7. Choosing a structure

| Requirement | Choice |
|---|---|
| Indexed access, append | `ArrayList` |
| Stack or queue | `ArrayDeque` |
| Membership testing | `HashSet` |
| Counting | `HashMap` + `merge(k, 1, Integer::sum)` |
| Grouping | `HashMap` + `computeIfAbsent` |
| **Ordered map with floor/ceiling** | `TreeMap` |
| Repeated smallest/largest | `PriorityQueue` |
| LRU cache | `LinkedHashMap` in access-order mode |
| Dense small-integer keys | plain `int[]` |

---

## 8. Constant-factor notes

- `Scanner` is several times slower than `BufferedReader` + `StringTokenizer`. Never use it in an OA.
- `System.out.println` in a loop flushes each call; batch into a `StringBuilder`.
- Autoboxing in a hot loop allocates an `Integer` per operation — use `int[]` over `List<Integer>` where it matters.
- `ArrayList<Integer>` is a list of *pointers*; `int[]` is contiguous and dramatically more cache-friendly.
- Pre-size collections when the count is known: `new ArrayList<>(n)`, `new HashMap<>(capacity)`.
- Streams allocate and are slower than an equivalent loop.
