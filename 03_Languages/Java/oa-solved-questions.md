# Java — Solved OA Questions

> 16 questions in OA style with every option explained. Java language questions cluster around **reference equality, autoboxing and overloaded method resolution** — places where the compiler is happy and the semantics surprise you.
> Cover the answers. **45 seconds** each.

---

**Q1.** What is printed?
```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
list.remove(1);
System.out.println(list);
```
(a) `[2, 3]`  (b) `[1, 3]`  (c) `[1, 2]`  (d) Compile error

<details><summary>Answer</summary>

**(b) `[1, 3]`.**

`List` declares both `remove(int index)` and `remove(Object o)`. The literal `1` is an `int`, so **overload resolution picks the index version** — Java prefers an exact primitive match over autoboxing. Index 1 holds the value 2, which is removed.

**To remove by value:** `list.remove(Integer.valueOf(1))`.

This is the most-asked Java collections question there is, and it is a real production bug, not just an exam curiosity.
</details>

---

**Q2.** What is printed?
```java
Integer a = 127, b = 127, c = 128, d = 128;
System.out.println((a == b) + " " + (c == d) + " " + c.equals(d));
```
(a) `true true true`  (b) `true false true`  (c) `false false true`  (d) `true false false`

<details><summary>Answer</summary>

**(b) `true false true`.**

Java caches boxed `Integer` objects for **−128 to 127**, so `a` and `b` are literally the same object and `==` (a reference comparison) is true. At 128 the cache ends, so `c` and `d` are distinct objects. `.equals()` compares values and is true in both cases.

**Never compare boxed numbers with `==`.** The bug hides during testing with small values and appears in production with large ones.
</details>

---

**Q3.** What is printed?
```java
int x = 100000 * 100000;
System.out.println(x);
```
(a) 10000000000  (b) 1410065408  (c) 0  (d) Compile error

<details><summary>Answer</summary>

**(b) 1410065408.**

Both operands are `int`, so the multiplication is performed in 32-bit arithmetic and **wraps silently** — there is no exception and no warning.

**The fix:** `long x = 100000L * 100000;` — cast *before* multiplying. Declaring the destination as `long` is not enough, because the expression is evaluated first.

Exactly the same trap as in C++, and the same wrapped value.
</details>

---

**Q4.** What is printed?
```java
String s1 = "abc";
String s2 = "ab" + "c";
String s3 = new String("abc");
String ab = "ab";
String s4 = ab + "c";
System.out.println((s1 == s2) + " " + (s1 == s3) + " " + (s1 == s4) + " " + s1.equals(s4));
```
(a) `true true true true`  (b) `true false false true`  (c) `false false false true`  (d) `true true false true`

<details><summary>Answer</summary>

**(b) `true false false true`.**

- `s2` is a **compile-time constant** expression, folded to `"abc"` and interned — the same object as `s1`.
- `new String(...)` forces a fresh heap object, so `s1 == s3` is false.
- `s4` is a **runtime** concatenation (because `ab` is a variable), producing a new object — so `s1 == s4` is false.
- `.equals` compares content and is true.

**The takeaway:** whether `==` on strings works depends on compile-time constant folding, which is exactly why you must never rely on it.
</details>

---

**Q5.** What is printed?
```java
Object o = true ? Integer.valueOf(1) : Double.valueOf(2.0);
System.out.println(o);
```
(a) `1`  (b) `1.0`  (c) `2.0`  (d) Compile error

<details><summary>Answer</summary>

**(b) `1.0`.**

The conditional operator applies **binary numeric promotion** to its two branches. With an `Integer` and a `Double`, both are unboxed, the result type becomes `double`, and the chosen value 1 is widened to 1.0 before being re-boxed into `Object`.

The condition really is true and the `Integer` branch really is taken — the type system changed the value anyway. This is the subtlest autoboxing trap in the language.
</details>

---

**Q6.** What is printed?
```java
System.out.println('a' + 1);
System.out.println((char) ('a' + 1));
System.out.println("" + 'a' + 1);
System.out.println('a' + 1 + "");
```
(a) `98 / b / a1 / 98`  (b) `b / b / a1 / a1`  (c) `98 / b / 98 / 98`  (d) `a1 / b / a1 / 98`

<details><summary>Answer</summary>

**(a) `98`, `b`, `a1`, `98`.**

1. `'a' + 1` promotes the char to its code 97 and adds → `98`.
2. The cast converts 98 back to a char → `b`.
3. `"" + 'a'` is string concatenation, giving `"a"`, then `+ 1` appends → `"a1"`.
4. `'a' + 1` is evaluated **first** (left to right) as arithmetic → 98, then `+ ""` stringifies → `"98"`.

The difference between the last two is purely the position of the empty string, which forces concatenation earlier or later.
</details>

---

**Q7.** `PriorityQueue<Integer> pq = new PriorityQueue<>();` is
(a) a max-heap  (b) a min-heap  (c) a sorted list  (d) unordered

<details><summary>Answer</summary>

**(b) a min-heap.**

Java's default is min; **C++'s `priority_queue` defaults to max** — the classic cross-language confusion.

Max-heap in Java: `new PriorityQueue<>(Collections.reverseOrder())`.

A second detail worth knowing: **iterating** a `PriorityQueue` does *not* produce sorted order. Only repeated `poll()` does.
</details>

---

**Q8.** What is the complexity of `s += c` inside a loop running n times?
(a) O(n)  (b) O(n log n)  (c) **O(n²)**  (d) O(1)

<details><summary>Answer</summary>

**(c) O(n²).**

`String` is immutable, so each `+=` compiles to a new `StringBuilder`, copies the whole accumulated string, appends, and calls `toString()`. Summed over n iterations that is 1 + 2 + … + n.

**The fix:** create one `StringBuilder` outside the loop and `append` into it.

Note that a *single* `a + b + c` expression is fine — the compiler fuses it into one `StringBuilder`. It is the loop that kills you.
</details>

---

**Q9.** Which is true about `Arrays.sort`?
(a) It is stable for primitives and unstable for objects
(b) It is unstable for primitives (dual-pivot quicksort) and stable for objects (Timsort)
(c) It is always stable
(d) It is always O(n log n) worst case

<details><summary>Answer</summary>

**(b).**

Primitive arrays use dual-pivot quicksort — not stable, and O(n²) in the adversarial worst case. Object arrays use Timsort — stable and guaranteed O(n log n).

Stability is meaningless for primitives (two equal `int`s are indistinguishable), which is why the library is free to use the faster unstable algorithm there.

(d) is false precisely because of the primitive path; boxing to `Integer[]` buys the guarantee if you need it.
</details>

---

**Q10.** What is printed?
```java
static void f(int[] a) {
    a[0] = 99;
    a = new int[]{7};
}
// in main:
int[] arr = {1};
f(arr);
System.out.println(arr[0]);
```
(a) 1  (b) 7  (c) 99  (d) 0

<details><summary>Answer</summary>

**(c) 99.**

Java is **pass by value** — but the value passed is the *reference*. Mutating the object through it (`a[0] = 99`) is visible to the caller; **reassigning the parameter** (`a = new int[]{7}`) only rebinds the local copy of the reference and is invisible.

"Java is pass by value, and for objects the value is a reference" is the precise phrasing interviewers look for.
</details>

---

**Q11.** What is printed?
```java
Map<String,Integer> m = new HashMap<>();
int x = m.get("missing");
System.out.println(x);
```
(a) 0  (b) null  (c) **NullPointerException**  (d) Compile error

<details><summary>Answer</summary>

**(c) NullPointerException.**

`get` returns `null` for a missing key, and assigning it to a primitive `int` triggers **unboxing of null** — which calls `intValue()` on a null reference.

**The fix:** `m.getOrDefault("missing", 0)`.

This is the most common NPE in real Java code, because the line looks completely innocuous.
</details>

---

**Q12.** What is printed?
```java
List<String> list = Arrays.asList("a", "b");
list.add("c");
System.out.println(list);
```
(a) `[a, b, c]`  (b) `[a, b]`  (c) **UnsupportedOperationException**  (d) Compile error

<details><summary>Answer</summary>

**(c) UnsupportedOperationException.**

`Arrays.asList` returns a **fixed-size view backed by the array** — `set` works, but `add` and `remove` throw.

**The fix:** `new ArrayList<>(Arrays.asList("a", "b"))`.

`List.of(...)` is stricter still: fully immutable, so even `set` throws.
</details>

---

**Q13.** What does `"a.b.c".split(".")` return?
(a) `["a", "b", "c"]`  (b) an empty array  (c) `["a.b.c"]`  (d) Compile error

<details><summary>Answer</summary>

**(b) an empty array.**

`split` takes a **regular expression**, and `.` matches any character — so every character is a delimiter, all the resulting fields are empty, and trailing empty strings are discarded, leaving nothing.

**The fix:** `split("\\.")`. The same escaping is needed for `|`, `+`, `*`, `?`, `(`, `)`, `[`, `]`, `^`, `$`.
</details>

---

**Q14.** What is printed?
```java
static int f() {
    int x = 1;
    try { return x; }
    finally { x = 2; }
}
System.out.println(f());
```
(a) 1  (b) 2  (c) 0  (d) Compile error

<details><summary>Answer</summary>

**(a) 1.**

The return **value** is computed and copied before `finally` runs, so modifying the local afterwards has no effect on what was already captured.

**Contrast with a `return` inside `finally`**, which *does* replace the pending value (and would print 2). That form also swallows pending exceptions, which is why compilers warn about it and you should never write it.
</details>

---

**Q15.** Why must `hashCode` be overridden whenever `equals` is?
(a) The compiler requires it
(b) Otherwise equal objects may land in different buckets and hash lookups fail silently
(c) It improves performance only
(d) It is optional

<details><summary>Answer</summary>

**(b).**

The contract is that equal objects must produce equal hash codes. Without it, two objects that compare `equals` inherit identity-based hash codes, hash to different buckets, and `HashSet.contains` returns false without ever calling `equals`.

It **compiles and runs** — the failure is silent, which is what makes it dangerous and why IDEs generate both methods together.
</details>

---

**Q16.** Which input method should you use in an OA with 10⁶ integers?
(a) `Scanner`  (b) `BufferedReader` + `StringTokenizer`  (c) `System.in.read()` directly  (d) They perform identically

<details><summary>Answer</summary>

**(b) `BufferedReader` + `StringTokenizer`.**

`Scanner` parses with regular expressions and synchronises on every call, making it several times slower — enough to TLE an otherwise correct solution on large input.

Pair it with batched output: append to a `StringBuilder` and print once, instead of calling `System.out.println` per line (each of which flushes).

(c) is faster still with a hand-rolled reader, but the gain over (b) is rarely worth the code.
</details>

---

## Scoring

| Score /16 | Reading |
|---|---|
| 14+ | Java fluency is OA-ready |
| 10–13 | Re-read `gotchas.md`; drill the equality and autoboxing items |
| 6–9 | Work `syntax-reference.md` and `library-complexities.md` properly |
| < 6 | Consider whether Java should be your primary language (`language-choice.md`) |

**Diagnostic:** misses on Q2, Q4, Q5 and Q11 all trace to autoboxing and reference equality. That single cluster is where Java candidates lose the most marks, and it is fixable in one focused session.
