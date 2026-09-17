# Bit Manipulation — Flashcards

## Questions

1. Give the three XOR properties behind the single-number trick.
2. What does `x & (x - 1)` do, and why?
3. What does `x & -x` do, and why?
4. How do you test for a power of two, and why is the `x > 0` guard required?
5. How do you count set bits in O(number of set bits)?
6. Give the O(n) recurrence for counting bits of every value 0..n.
7. How do you solve "two elements appear once, all others twice"?
8. How do you enumerate all subsets of n elements with a bitmask?
9. How do you enumerate all submasks of a mask, and what is the total cost over all masks?
10. What is `~x` in terms of `-x`?
11. Why is `1 << 31` a bug on a 32-bit int?
12. Why does `while (x) x >>= 1` hang on a negative in Java, and what fixes it?
13. Why does Python need `0xFFFFFFFF` masking to simulate 32-bit arithmetic?
14. Why is `x & 1 == 0` wrong in C++?
15. Give the mask expressions for "n low bits set" and "complement within n bits".

---

## Answers

1. `x ^ x = 0`, `x ^ 0 = x`, and XOR is commutative and associative — so duplicates cancel regardless of order.
2. Clears the lowest set bit. Subtracting 1 flips that bit to 0 and sets everything below it to 1; the AND keeps only the higher bits.
3. Isolates the lowest set bit. In two's complement `-x = ~x + 1`, which matches x exactly at the lowest set bit and differs everywhere above.
4. `x > 0 and (x & (x - 1)) == 0`. Without the guard, 0 passes (it has no set bits) and negatives can too.
5. Brian Kernighan's loop: `while x: x &= x - 1; count += 1` — each iteration removes exactly one set bit.
6. `dp[i] = dp[i >> 1] + (i & 1)`, or equivalently `dp[i] = dp[i & (i-1)] + 1`.
7. XOR everything to get `a ^ b`; take `d = xor & -xor` (a bit where they must differ); partition the array by that bit and XOR each partition separately.
8. Iterate `mask` over `range(1 << n)`; element i is included iff `mask >> i & 1`.
9. `sub = mask; while sub: process(sub); sub = (sub - 1) & mask`, plus the empty mask. Summed over all masks it is 3ⁿ.
10. `~x == -x - 1`.
11. On a signed 32-bit int, bit 31 is the sign bit, so the shift overflows — undefined behaviour in C++. Use `1LL << 31`.
12. `>>` is an arithmetic shift, so a negative keeps its sign bit and never reaches zero. Use the logical shift `>>>`.
13. Python integers are unbounded and treat negatives as having infinitely many leading ones, so a carry loop never terminates without explicit masking and a manual signed conversion.
14. `&` binds looser than `==`, so it parses as `x & (1 == 0)`, i.e. `x & 0`. Write `(x & 1) == 0`.
15. `(1 << n) - 1` sets the n low bits; `mask ^ ((1 << n) - 1)` complements within n bits.
