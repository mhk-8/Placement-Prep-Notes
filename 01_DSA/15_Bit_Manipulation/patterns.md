# Bit Manipulation — Templates

## 1. Single bit operations
```python
def get_bit(x, i):    return (x >> i) & 1
def set_bit(x, i):    return x | (1 << i)
def clear_bit(x, i):  return x & ~(1 << i)
def toggle_bit(x, i): return x ^ (1 << i)
```

## 2. Counting set bits
```python
def popcount(x):                      # Brian Kernighan: O(set bits)
    c = 0
    while x:
        x &= x - 1; c += 1
    return c

bin(x).count('1')                     # Python built-in
x.bit_count()                         # Python 3.10+
```
C++: `__builtin_popcount(x)` / `__builtin_popcountll(x)`. Java: `Integer.bitCount(x)`.

**Counting Bits for 0..n in O(n)**
```python
dp = [0] * (n + 1)
for i in range(1, n + 1):
    dp[i] = dp[i >> 1] + (i & 1)      # or dp[i & (i-1)] + 1
```

## 3. Power of two / four
```python
def is_power_of_two(x):  return x > 0 and (x & (x - 1)) == 0
def is_power_of_four(x): return is_power_of_two(x) and (x & 0x55555555) != 0
```

## 4. Single Number family
```python
def single_number(nums):              # every other element appears twice
    r = 0
    for x in nums: r ^= x
    return r

def missing_number(nums):             # values 0..n, one missing
    r = len(nums)
    for i, x in enumerate(nums): r ^= i ^ x
    return r

def single_number_iii(nums):          # exactly two singles
    xor = 0
    for x in nums: xor ^= x
    d = xor & -xor                    # a bit where the two singles differ
    a = b = 0
    for x in nums:
        if x & d: a ^= x
        else:     b ^= x
    return [a, b]

def single_number_ii(nums):           # every other appears three times
    ones = twos = 0
    for x in nums:
        ones = (ones ^ x) & ~twos
        twos = (twos ^ x) & ~ones
    return ones
```

## 5. Subsets via bitmask
```python
n = len(a)
for mask in range(1 << n):
    sub = [a[i] for i in range(n) if mask >> i & 1]
    ...
```

**Iterate all submasks of `mask`** (total 3ⁿ over all masks):
```python
sub = mask
while sub:
    process(sub)
    sub = (sub - 1) & mask
process(0)                            # the empty submask, if wanted
```

## 6. Reverse bits (32-bit)
```python
def reverse_bits(x):
    r = 0
    for _ in range(32):
        r = (r << 1) | (x & 1)
        x >>= 1
    return r
```

## 7. Add two integers without `+`
```python
def get_sum(a, b):
    MASK, MAX = 0xFFFFFFFF, 0x7FFFFFFF
    while b & MASK:
        carry = ((a & b) << 1) & MASK
        a = (a ^ b) & MASK            # sum without carry
        b = carry
    return a if a <= MAX else ~(a ^ MASK)     # back to signed
```
In C++ this is simply `while (b) { int c = (a & b) << 1; a ^= b; b = c; }`.

## 8. Bitmask DP skeleton (TSP shape)
```python
full = (1 << n) - 1
dp = [[INF]*n for _ in range(1 << n)]
dp[1][0] = 0
for mask in range(1 << n):
    for u in range(n):
        if not (mask >> u & 1) or dp[mask][u] == INF: continue
        for v in range(n):
            if mask >> v & 1: continue
            dp[mask | (1 << v)][v] = min(dp[mask | (1 << v)][v],
                                         dp[mask][u] + cost[u][v])
```

## 9. Bitset as a set of small integers
```python
s = 0
s |= 1 << x                # add x
s &= ~(1 << x)             # remove x
present = s >> x & 1       # test
size = bin(s).count('1')   # cardinality
union, inter, diff = s | t, s & t, s & ~t
```

## 10. Useful masks
```python
(1 << n) - 1               # n low bits set
mask ^ ((1 << n) - 1)      # complement within n bits
x & -x                     # lowest set bit
x & (x - 1)                # x without its lowest set bit
x | (x - 1)                # fill all bits below the lowest set bit
```

## C++ notes
- `1LL << k` for k ≥ 31. `1 << 31` on an `int` is undefined behaviour.
- `__builtin_clz(x)` counts leading zeros (undefined for x = 0); `31 - __builtin_clz(x)` is ⌊log₂ x⌋.
- `bitset<N>` gives a fixed-size bit array with `count()`, `test()`, `set()` and full bitwise operators.
- Precedence: `&`, `^`, `|` bind looser than `==`. Always parenthesise.
