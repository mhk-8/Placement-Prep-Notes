# C++ — Gotchas

> The traps that produce a wrong answer, a TLE or a crash without an obvious cause. Read before every submission.

---

## Overflow

- **`int * int` overflows even when stored in a `long long`.** The multiplication happens in `int` first. Write `(ll)a * b`.
- `1 << 31` on a signed 32-bit `int` is undefined behaviour. Use `1LL << k` for any k ≥ 31.
- `mid = (lo + hi) / 2` overflows near 2³¹. Always `lo + (hi - lo) / 2`.
- `accumulate(all(a), 0)` sums in `int`. Pass `0LL`.
- Prefix sums over 10⁵ values of 10⁹ each reach 10¹⁴ — `long long`.
- `abs(INT_MIN)` overflows, and so does negating it.
- Comparing signed with unsigned promotes the signed value: `if (i < v.size() - 1)` is a disaster when `v` is empty, because `size() - 1` wraps to a huge unsigned. Cast: `(int)v.size()`.

## Undefined behaviour

- `st.top()`, `q.front()`, `v.back()` on an empty container — UB, not an exception.
- `v[i]` out of range does **not** bounds-check. `v.at(i)` throws, but is slower.
- Returning a reference or pointer to a local variable.
- Reading an uninitialised variable — `int x;` inside a function is garbage, not 0. (Globals *are* zero-initialised.)
- Modifying a container while iterating it; `erase` invalidates iterators (`erase` returns the next valid one).
- A comparator returning `<=` instead of `<` — breaks strict weak ordering and can make `std::sort` read out of bounds and **segfault**.
- `__builtin_clz(0)` and `__builtin_ctz(0)` are undefined.

## Containers

- **`mp[k]` inserts** a default-constructed value when `k` is absent. Inside a membership test this silently grows the map. Use `.count()` or `.find()`.
- **`lower_bound(s.begin(), s.end(), x)` on a `set` or `map` is O(n).** Use the member `s.lower_bound(x)`.
- `multiset.erase(x)` erases **every** copy. For one: `s.erase(s.find(x))`.
- `unique()` removes only *consecutive* duplicates — sort first, and remember `a.erase(unique(all(a)), a.end())`.
- `v.erase(it)` is O(n) for a vector.
- `vector<bool>` is a bit-packed specialisation, not a real container of bools; `auto& b = v[i]` does not behave as expected, and per-element access is slow. Use `vector<char>`.
- Iterators are invalidated by reallocation on `push_back`.
- `unordered_map` with `pair` keys does not compile without a custom hash.

## Strings

- `s.substr(pos, len)` takes a **length**, not an end index.
- `s.find(...)` returns `string::npos` (a huge unsigned), not −1. Compare against `string::npos`.
- `cin >> s` stops at whitespace; `getline` reads the line. After `cin >> n`, call `cin.ignore()` before `getline`, or you read the leftover newline.
- `char` arithmetic overflows silently: `'a' + 30` is fine as an int but wraps if stored in a `char`.
- `s[i] == '1'` compares characters; `s[i] == 1` compares against the control character 0x01.

## Arithmetic

- Integer division **truncates toward zero**: `-7 / 2 == -3` in C++ but `-4` in Python. `%` follows: `-7 % 2 == -1`.
- Normalise a modulus with `((x % m) + m) % m` whenever negatives are possible.
- `pow()` returns a `double` — `pow(10, 9)` can come back as 999999999.999. Use integer exponentiation.
- Never compare floats with `==`; use `fabs(a - b) < 1e-9`.
- `sqrt(n)` in a loop condition can miss a boundary through rounding. Prefer `i * i <= n`.

## Performance

- `endl` inside a loop flushes the stream every iteration — use `'\n'`.
- Missing `ios_base::sync_with_stdio(false)` makes `cin`/`cout` several times slower.
- Passing a large container by value copies it on every call.
- `vector` reallocation without `reserve` in a 10⁶-element push loop.
- Recursive DFS on a 10⁵-node path graph can overflow the ~1 MB default stack. Go iterative.
- Large local arrays (`int a[1000000];` inside a function) also blow the stack — declare them globally.
- `unordered_map` is vulnerable to anti-hash tests on adversarial judges; use a randomised custom hash.

## Precedence and syntax

- `&`, `^`, `|` bind **looser** than `==`. `x & 1 == 0` parses as `x & (1 == 0)`. Parenthesise.
- `<<` on a stream versus a shift: `cout << a << b` is fine, but `cout << a & b` is not what you meant.
- A missing `break` in a `switch` falls through.
- `if (a = b)` assigns and tests, compiling with only a warning.
- Two adjacent closing angle brackets were a parse error pre-C++11 (`vector<vector<int>>`); fine now, but old judges may complain.

## The five that cost the most marks

1. `int` overflow on a product
2. `mp[k]` inserting during a membership test
3. `endl` in a loop causing a TLE on an otherwise correct solution
4. The free `lower_bound` on a `set` turning O(log n) into O(n)
5. `substr(pos, len)` read as `substr(start, end)`
