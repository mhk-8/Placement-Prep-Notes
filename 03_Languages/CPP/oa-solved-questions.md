# C++ — Solved OA Questions

> 16 questions in OA style, with every option explained. C++ language questions are almost all about **overflow, container semantics and undefined behaviour** — the three places where the compiler will not save you.
> Cover the answers. **45 seconds** each.

---

**Q1.** What is printed?
```cpp
int a = 100000, b = 100000;
long long c = a * b;
cout << c;
```
(a) 10000000000  (b) 1410065408  (c) 0  (d) Compile error

<details><summary>Answer</summary>

**(b) 1410065408.**

The multiplication `a * b` happens in **`int`** arithmetic *before* the result is assigned to a `long long`. 10¹⁰ does not fit in 32 bits, so it wraps; the truncated value is then widened.

**The fix:** cast one operand first — `long long c = (long long)a * b;`

This is the single most common C++ bug in OAs, because the code *looks* like it accounts for the size. The declared type of the destination never changes how the expression is evaluated.
</details>

---

**Q2.** What is printed?
```cpp
map<int,int> mp;
mp[1] = 10;
if (mp[2] == 0) cout << mp.size();
```
(a) 1  (b) 2  (c) 0  (d) Nothing

<details><summary>Answer</summary>

**(b) 2.**

`mp[2]` **inserts** a default-constructed value (0) when the key is absent, and returns a reference to it. So the condition is true *and* the map has grown to two entries.

**Use `mp.count(2)` or `mp.find(2) != mp.end()`** for membership. This trap corrupts frequency counts and silently inflates memory on large inputs.
</details>

---

**Q3.** What is printed?
```cpp
vector<int> v = {1, 2, 3};
cout << v.size() - 1 << " ";
v.clear();
cout << (v.size() - 1 > 100);
```
(a) 2 0  (b) 2 1  (c) 3 0  (d) Compile error

<details><summary>Answer</summary>

**(b) 2 1.**

`size()` returns an **unsigned** `size_t`. With three elements, `3 - 1 = 2` ✔. After `clear()`, `0 - 1` **wraps around** to 18446744073709551615, which is comfortably greater than 100 — so the comparison prints 1.

**The practical damage:** `for (int i = 0; i < v.size() - 1; i++)` iterates about 1.8 × 10¹⁹ times on an empty vector. Cast it: `(int)v.size() - 1`.
</details>

---

**Q4.** What is printed?
```cpp
cout << -7 / 2 << " " << -7 % 2;
```
(a) -4 1  (b) -3 -1  (c) -4 -1  (d) -3 1

<details><summary>Answer</summary>

**(b) -3 -1.**

C++ integer division **truncates toward zero**, and `%` follows so that `(a/b)*b + a%b == a`.

**Python differs:** `-7 // 2` is −4 and `-7 % 2` is 1, because Python floors toward −∞. Porting hash-bucket or cyclic-index code between the two silently breaks unless you normalise with `((x % m) + m) % m`.

(a) is the Python answer, which is exactly why it is offered.
</details>

---

**Q5.** What is printed?
```cpp
string s = "hello world";
cout << s.substr(2, 3);
```
(a) llo  (b) ll  (c) llo w  (d) lo

<details><summary>Answer</summary>

**(a) llo.**

`substr(pos, len)` takes a **length**, not an end index — three characters starting at index 2.

(b) is what you would get if it took an end index, which is how Python's `s[2:3]` behaves. Mixing the two conventions is a frequent source of off-by-one bugs when moving between languages.
</details>

---

**Q6.** What is printed?
```cpp
vector<int> v = {3, 1, 2, 1, 3};
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
cout << v.size();
```
(a) 5  (b) 3  (c) 4  (d) 2

<details><summary>Answer</summary>

**(b) 3.**

After sorting: `1 1 2 3 3`. `unique` removes **consecutive** duplicates and returns an iterator to the new logical end; `erase` then trims the tail. Result: `1 2 3`.

**Both halves matter.** Without the sort, `unique` on `3 1 2 1 3` removes nothing. Without the `erase`, `size()` is still 5 — `unique` does not shrink the container.
</details>

---

**Q7.** `priority_queue<int> pq;` is
(a) a min-heap  (b) a max-heap  (c) unordered  (d) a sorted vector

<details><summary>Answer</summary>

**(b) a max-heap.**

C++ defaults to max; **Java's `PriorityQueue` defaults to min**, which is the cross-language trap.

Min-heap in C++: `priority_queue<int, vector<int>, greater<int>> pq;`

And the comparator reads backwards: it must return true when the first argument has *lower* priority, so `greater<int>` produces a **min**-heap.
</details>

---

**Q8.** What is printed?
```cpp
int x = 5;
cout << (x & 1 == 0);
```
(a) 1  (b) 0  (c) 5  (d) Compile error

<details><summary>Answer</summary>

**(b) 0.**

`==` binds **tighter** than `&`, so this parses as `x & (1 == 0)` → `5 & 0` → `0`.

The intent was `(x & 1) == 0`, which for x = 5 would also be 0 (5 is odd) — but for x = 4 the buggy version still prints 0 while the correct one prints 1. **Always parenthesise bitwise operations.**
</details>

---

**Q9.** What is the complexity of `lower_bound(s.begin(), s.end(), x)` where `s` is a `std::set`?
(a) O(log n)  (b) O(n)  (c) O(1)  (d) O(n log n)

<details><summary>Answer</summary>

**(b) O(n).**

The free `std::lower_bound` performs binary search only on **random-access** iterators. A `set` has bidirectional iterators, so the algorithm degrades to a linear walk.

**Use the member function `s.lower_bound(x)`**, which descends the red-black tree in O(log n). The same applies to `map`.

This one is genuinely dangerous: the code compiles, gives the right answer, and turns an O(n log n) solution into O(n²).
</details>

---

**Q10.** What is printed?
```cpp
vector<int> v(100000, 100000);
cout << accumulate(v.begin(), v.end(), 0);
```
(a) 10000000000  (b) 1410065408  (c) 100000  (d) 0

<details><summary>Answer</summary>

**(b) 1410065408.**

`accumulate`'s accumulator type comes from the **initial value**. Passing `0` (an `int`) sums in `int`, so 10¹⁰ wraps.

**The fix:** `accumulate(v.begin(), v.end(), 0LL)`.

Same wrapped value as Q1, because it is the same 10¹⁰ truncated to 32 bits — a useful thing to notice when a total looks implausible.
</details>

---

**Q11.** This code is
```cpp
vector<int> v{1, 2, 3, 4, 5};
for (auto it = v.begin(); it != v.end(); ++it)
    if (*it % 2 == 0) v.erase(it);
```
(a) correct  (b) undefined behaviour — `erase` invalidates the iterator  (c) a compile error  (d) an infinite loop always

<details><summary>Answer</summary>

**(b) undefined behaviour.**

`erase` invalidates `it` (and everything after it), so the subsequent `++it` operates on a dangling iterator.

**The two correct forms:**
```cpp
for (auto it = v.begin(); it != v.end(); )
    if (*it % 2 == 0) it = v.erase(it);   // erase returns the next valid iterator
    else              ++it;

// or the idiomatic one-liner
v.erase(remove_if(v.begin(), v.end(), [](int x){ return x % 2 == 0; }), v.end());
```
</details>

---

**Q12.** What is printed?
```cpp
cout << 5 / 2 << " " << 5.0 / 2 << " " << 5 / 2.0;
```
(a) 2 2.5 2.5  (b) 2.5 2.5 2.5  (c) 2 2 2  (d) 2.5 2 2.5

<details><summary>Answer</summary>

**(a) 2 2.5 2.5.**

Integer division when **both** operands are integers; floating-point division as soon as either one is a double, because the other is promoted.

**The practical rule:** to get a real average, write `sum / (double)count` or `1.0 * sum / count` — `sum / count` truncates silently.
</details>

---

**Q13.** Which is true of `std::sort`?
(a) It is stable  (b) It is guaranteed O(n log n) worst case  (c) Both  (d) Neither in the way described

<details><summary>Answer</summary>

**(b) — it is O(n log n) worst case, but not stable.**

`std::sort` is introsort: quicksort that switches to heapsort when recursion gets too deep, with insertion sort for small ranges. The heapsort fallback guarantees the worst-case bound; nothing guarantees stability.

**For stability use `std::stable_sort`** (merge sort). Contrast with Java, where object sorting uses stable Timsort but primitive sorting does not.
</details>

---

**Q14.** What is printed?
```cpp
vector<int> a = {1, 2, 3};
auto b = a;
b[0] = 99;
cout << a[0] << b[0];
```
(a) 99 99  (b) 1 99  (c) 1 1  (d) Compile error

<details><summary>Answer</summary>

**(b) 1 99.**

`auto b = a;` **copies** the vector — C++ containers have value semantics, unlike Java and Python references. Modifying `b` leaves `a` untouched.

**The performance corollary:** that copy is O(n). Inside a function call it happens on every invocation, which is why containers are passed as `const vector<int>&`. Use `auto& b = a;` when you want an alias.
</details>

---

**Q15.** Why is `endl` discouraged inside a loop?
(a) It prints the wrong character
(b) It flushes the output buffer every call
(c) It is slower to compile
(d) It is deprecated

<details><summary>Answer</summary>

**(b) it flushes every call.**

`endl` is `'\n'` plus `flush()`. A flush per iteration over 10⁵ lines turns a fast solution into a TLE.

Use `'\n'`, and combine it with `ios_base::sync_with_stdio(false); cin.tie(nullptr);`. Together these three changes routinely cut I/O-bound runtimes by an order of magnitude.
</details>

---

**Q16.** What does this print?
```cpp
int arr[3];
cout << arr[0];
```
declared **inside `main`** versus declared **globally**?
(a) 0 in both cases  (b) garbage in both  (c) garbage inside `main`, 0 globally  (d) compile error inside `main`

<details><summary>Answer</summary>

**(c) garbage inside `main`, 0 globally.**

Objects with **static storage duration** (globals and `static` locals) are zero-initialised before the program starts. Automatic (stack) variables are not — reading one before assignment is undefined behaviour, and on many toolchains it happens to be 0 in debug builds and garbage in release, which is how this hides during testing.

**The habit:** always initialise — `int arr[3] = {};` or `vector<int> v(3);`.
</details>

---

## Scoring

| Score /16 | Reading |
|---|---|
| 14+ | C++ fluency is OA-ready |
| 10–13 | Re-read `gotchas.md`; drill the overflow and container items |
| 6–9 | Work `syntax-reference.md` and `library-complexities.md` properly |
| < 6 | Consider whether C++ should be your primary language (`language-choice.md`) |

**Diagnostic:** misses on Q1, Q3 and Q10 mean the integer-promotion rules have not landed — that cluster alone accounts for more lost OA marks than any algorithmic gap.
