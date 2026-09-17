# Complexity & Math — Pitfalls

## Overflow
- `mid = (lo + hi) / 2` overflows in C++/Java when lo and hi are near 2³¹. **Always** write `lo + (hi - lo) / 2`.
- `a * b` where both fit in `int` but the product does not. Cast one operand: `(long long)a * b`.
- `lcm(a, b) = a * b / gcd(a, b)` overflows. Write `a / gcd * b`.
- Prefix sums over 10⁵ values of 10⁹ each reach 10¹⁴ — needs 64-bit.
- `1 << 31` is undefined for a 32-bit signed int. Use `1LL << k`.
- `abs(INT_MIN)` overflows; so does negating it.

## Modular arithmetic
- Forgetting `+ m` after a subtraction, leaving a negative residue.
- Taking the mod only at the end instead of after every operation.
- Attempting `(a / b) % m` directly — division needs the modular inverse.
- Using Fermat's inverse when the modulus is **not** prime.
- Computing `pow(fact[r] * fact[n-r], MOD-2, MOD)` per query inside a loop — O(n log p) when an O(n) backward pass gives O(1) queries.

## Sieve
- Starting the inner loop at `2*i` rather than `i*i` (correct but slower).
- Looping the outer index to n instead of √n.
- Off-by-one on the array size — a sieve up to n needs n+1 slots.
- Forgetting that 0 and 1 are not prime.

## Language-specific
- `%` on a negative number is negative in C++/Java, non-negative in Python. This silently breaks hash-bucket and cyclic-index code ported between languages.
- Python integer division `//` floors towards −∞; C++ `/` truncates towards 0. `-7 // 2` is −4 in Python, −3 in C++.
- Python has no overflow, but arbitrary-precision arithmetic is slow — a loop building a 10⁶-digit number will TLE.

## Reasoning traps
- Claiming hash-map lookup is O(1) **worst case** — it is O(1) average, O(n) worst.
- Saying "O(n log n)" for a heap build; **building** a heap is O(n), while n successive pushes are O(n log n).
- Ignoring the cost of the *comparison* itself: sorting n strings of length L is O(n·L·log n), not O(n log n).
- Treating O(2ⁿ) with n = 20 as infeasible — 10⁶ is fine. Treating O(n²) with n = 10⁵ as fine — 10¹⁰ is not.
- Quoting average-case complexity when the question asks for worst case (quicksort, hashing).

## Floating point
- Comparing floats with `==`.
- Binary searching on reals with a `while (lo < hi)` condition instead of a fixed iteration count — it may never terminate.
- Using `sqrt(n)` in a loop condition: floating-point rounding can make `i <= sqrt(n)` miss the boundary. Prefer `i * i <= n`.
