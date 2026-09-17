# Bit Manipulation — Concepts

## 1. Core idea in 3 lines
Bit tricks let you treat an integer as a set of up to 64 booleans and operate on all of them in one instruction. In OAs they show up as cheap MCQ points and as the O(1)-space unlock for a handful of array problems. The whole topic is about ten identities plus knowing how your language handles signs and shifts.

---

## 2. The operators

| Op | Effect | Common use |
|---|---|---|
| `&` | 1 where both are 1 | masking, testing a bit |
| `\|` | 1 where either is 1 | setting a bit |
| `^` | 1 where exactly one is 1 | toggling, pairing-off |
| `~` | flips every bit | `~x == -x - 1` in two's complement |
| `<<k` | shift left | multiply by 2ᵏ |
| `>>k` | shift right | divide by 2ᵏ (floor, for non-negatives) |
| `>>>k` | logical right shift (Java) | shift in zeros regardless of sign |

### XOR — the properties that solve problems
- `x ^ x = 0`, `x ^ 0 = x`
- commutative and associative → **order does not matter**
- `a ^ b ^ b = a` → XOR is its own inverse

Hence: XOR the whole array and everything appearing twice cancels, leaving the single number. The same idea gives the missing number (`XOR of 0..n` XOR `XOR of the array`), swapping without a temporary, and prefix-XOR range queries.

---

## 3. The idioms to memorise

| Goal | Expression |
|---|---|
| Test bit i | `(x >> i) & 1` |
| Set bit i | `x \| (1 << i)` |
| Clear bit i | `x & ~(1 << i)` |
| Toggle bit i | `x ^ (1 << i)` |
| **Clear the lowest set bit** | `x & (x - 1)` |
| **Isolate the lowest set bit** | `x & -x` |
| Set all bits below the lowest set bit | `x \| (x - 1)` |
| Is a power of two | `x > 0 and (x & (x - 1)) == 0` |
| Count set bits (Kernighan) | loop `x &= x - 1`, counting — O(number of set bits) |
| Is odd | `x & 1` |
| Multiply / divide by 2ᵏ | `x << k` / `x >> k` |
| Swap without a temp | `a ^= b; b ^= a; a ^= b` |
| Lowest set bit's index | `x.bit_length() - 1` of `x & -x` |

**Why `x & (x-1)` clears the lowest set bit:** subtracting 1 flips the lowest set bit to 0 and turns every bit below it to 1; ANDing keeps only the bits above, which is the original minus that bit. **Why `x & -x` isolates it:** `-x` is `~x + 1`, so it agrees with `x` above the lowest set bit inverted and matches exactly at it.

`x & -x` is what makes a Fenwick tree work (`17_Advanced_DS`).

---

## 4. Subsets via bitmask

For n ≤ 20, a mask in `[0, 2ⁿ)` encodes a subset: bit i set means element i is included.

```python
for mask in range(1 << n):
    subset = [a[i] for i in range(n) if mask >> i & 1]
```

- **Number of subsets:** `1 << n`
- **Iterate all submasks of a mask:** `sub = mask; while sub: ...; sub = (sub - 1) & mask` — total cost over all masks is 3ⁿ
- **Complement within n bits:** `mask ^ ((1 << n) - 1)`
- **Popcount:** `bin(mask).count('1')` in Python, `__builtin_popcount` in C++, `Integer.bitCount` in Java

This is the foundation of bitmask DP (`13`).

---

## 5. The single-number family

| Problem | Method |
|---|---|
| Every element twice except one | XOR everything |
| Missing number in 0..n | XOR indices and values together |
| Every element three times except one | Count bits mod 3 per position, or two accumulators `ones`/`twos` |
| **Two** elements appear once, rest twice | XOR all → `x^y`; isolate a differing bit with `d = xor & -xor`; partition the array on that bit and XOR each group |

That last one is the standard hard variant and the reason `x & -x` is worth memorising.

---

## 6. Language and sign traps

**Two's complement:** negative numbers store `2ⁿ − |x|`. So `~x == -x - 1`, and the sign bit is the most significant bit.

| | Python | C++ | Java |
|---|---|---|---|
| Integer width | arbitrary | fixed (32/64) | fixed |
| `>>` on negatives | arithmetic, infinite sign extension | arithmetic (implementation-defined pre-C++20) | arithmetic; `>>>` is logical |
| `1 << 31` | fine | **overflow** on `int` — use `1LL << 31` | overflow on `int` — use `1L << 31` |
| `x & 0xFFFFFFFF` | needed to simulate 32-bit | implicit | implicit |

In Python, negative numbers behave as if they had infinitely many leading 1s, so 32-bit simulation requires explicit masking with `0xFFFFFFFF` and a manual conversion back to a signed value. This is what makes "Sum of Two Integers without +" awkward in Python and trivial in C++.

**Operator precedence:** `&`, `|`, `^` bind *looser* than `==` and `+` in C, C++ and Java. `x & 1 == 0` parses as `x & (1 == 0)`. Always parenthesise: `(x & 1) == 0`.

---

## 7. Recall questions

1. What are the three XOR properties that make the single-number trick work?
2. Why does `x & (x - 1)` clear the lowest set bit?
3. Why does `x & -x` isolate it?
4. How do you test whether x is a power of two, and why is the `x > 0` guard needed?
5. How do you enumerate all subsets with a bitmask?
6. How do you iterate all submasks of a mask, and what is the total cost over all masks?
7. Solve "two numbers appear once, the rest twice".
8. What is `~x` in terms of `-x`?
9. Why is `1 << 31` a bug in C++ and Java?
10. Why does `x & 1 == 0` misbehave in C-family languages?
