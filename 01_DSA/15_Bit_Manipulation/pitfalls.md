# Bit Manipulation — Pitfalls

## Precedence
- `x & 1 == 0` in C/C++/Java parses as `x & (1 == 0)` because `&` binds looser than `==`. Write `(x & 1) == 0`.
- `a | b << 2` is `a | (b << 2)` — correct here, but shifts bind tighter than `&`, `^` and `|`, which surprises people the other way. Parenthesise everything.

## Overflow and width
- `1 << 31` on a 32-bit `int` is undefined behaviour in C++ and overflows in Java. Use `1LL << 31` / `1L << 31`.
- Shifting by an amount ≥ the type width is undefined in C++.
- `x << k` silently losing high bits when the result exceeds the type.

## Signs
- `>>` on a negative number is an *arithmetic* shift in C++/Java/Python — it preserves the sign, so it never reaches 0. Loops of the form `while (x) x >>= 1` never terminate on negatives in Java; use `>>>`.
- Python integers are arbitrary precision, so negatives behave as if they have infinitely many leading ones. Simulating 32-bit arithmetic requires masking with `0xFFFFFFFF` and converting back to signed at the end.
- `abs(INT_MIN)` overflows; so does negating it.
- `~x` is `-x - 1`, not `-x`.

## Logic errors
- Forgetting the `x > 0` guard in the power-of-two test: `0 & (0-1) == 0`, so 0 would be reported as a power of two. Negative numbers also slip through.
- Using `x & (x-1)` when you meant `x & -x` (clearing versus isolating).
- In Single Number III, partitioning on a bit where the two singles happen to agree — you must pick a bit from `xor`, which by construction differs.
- Assuming XOR gives the missing number when elements can repeat more than twice.

## Bitmask enumeration
- Iterating `range(1 << n)` with n > ~25 — 2²⁵ is already 33 million.
- `mask >> i & 1` versus `mask & (1 << i)`: both work, but the second returns `1 << i`, not 1, so comparing it to `== 1` fails.
- Forgetting the empty submask when enumerating submasks (the `(sub-1) & mask` loop exits before processing 0).
- Mixing up the mask's bit order with the array's index order.

## General
- Reaching for bit tricks where clarity matters more; `x * 2` is not slower than `x << 1` on any modern compiler.
- Claiming popcount is O(1) when the loop version is O(number of set bits) — the hardware instruction is O(1), a manual loop is not.
