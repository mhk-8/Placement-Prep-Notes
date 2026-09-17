# Complexity & Math — Templates

## 1. GCD / LCM
```python
from math import gcd
def lcm(a, b): return a // gcd(a, b) * b     # divide first: avoids overflow
```
```cpp
long long g = __gcd(a, b);
long long l = a / g * b;                      // C++17: std::gcd, std::lcm in <numeric>
```

## 2. Extended Euclid — ax + by = gcd(a,b)
```python
def extgcd(a, b):
    if b == 0: return (a, 1, 0)
    g, x1, y1 = extgcd(b, a % b)
    return (g, y1, x1 - (a // b) * y1)
```

## 3. Fast exponentiation
```python
pow(base, exp, MOD)                           # built in, O(log exp)
```
```cpp
long long power(long long b, long long e, long long m){
    long long r = 1; b %= m;
    while (e > 0){ if (e & 1) r = r * b % m; b = b * b % m; e >>= 1; }
    return r;
}
```

## 4. Modular inverse
```python
MOD = 10**9 + 7
inv = pow(x, MOD - 2, MOD)          # MOD prime, gcd(x, MOD) = 1  (Fermat)
# non-prime modulus: use extended Euclid
def modinv(a, m):
    g, x, _ = extgcd(a, m)
    if g != 1: return None          # no inverse exists
    return x % m
```

## 5. Sieve of Eratosthenes
```python
def sieve(n):
    is_p = bytearray([1]) * (n + 1)
    is_p[0:2] = b'\x00\x00'
    for i in range(2, int(n**0.5) + 1):
        if is_p[i]:
            is_p[i*i : n+1 : i] = bytearray(len(range(i*i, n+1, i)))
    return [i for i in range(n + 1) if is_p[i]]
```

**Smallest-prime-factor sieve** — factorise any m ≤ n in O(log m):
```python
def spf_sieve(n):
    spf = list(range(n + 1))
    for i in range(2, int(n**0.5) + 1):
        if spf[i] == i:
            for j in range(i*i, n + 1, i):
                if spf[j] == j: spf[j] = i
    return spf

def factorise(m, spf):
    f = {}
    while m > 1:
        p = spf[m]
        while m % p == 0: f[p] = f.get(p, 0) + 1; m //= p
    return f
```

## 6. Single-number primality & factorisation — O(√n)
```python
def is_prime(n):
    if n < 2: return False
    if n < 4: return True
    if n % 2 == 0 or n % 3 == 0: return False
    i = 5
    while i * i <= n:
        if n % i == 0 or n % (i + 2) == 0: return False
        i += 6
    return True
```

## 7. nCr mod p
```python
MOD = 10**9 + 7
N = 200001
fact = [1] * N
for i in range(1, N): fact[i] = fact[i-1] * i % MOD
inv_fact = [1] * N
inv_fact[N-1] = pow(fact[N-1], MOD-2, MOD)
for i in range(N-1, 0, -1): inv_fact[i-1] = inv_fact[i] * i % MOD

def nCr(n, r):
    if r < 0 or r > n: return 0
    return fact[n] * inv_fact[r] % MOD * inv_fact[n-r] % MOD
```
O(N) precompute, **O(1) per query** — always precompute the inverse factorials backwards like this rather than calling `pow` per query.

## 8. Binary search on reals
```python
lo, hi = 0.0, 1e9
for _ in range(100):                  # fixed iterations — never test equality
    mid = (lo + hi) / 2
    if feasible(mid): hi = mid
    else:             lo = mid
return lo
```

## 9. Digit manipulation
```python
def digits(n):            return [int(c) for c in str(abs(n))]
def digit_sum(n):         return sum(int(c) for c in str(abs(n)))
def reverse_num(n):
    r = 0
    while n: r = r * 10 + n % 10; n //= 10
    return r
```

## C++ notes
- `__gcd(a,b)` (GCC) or `std::gcd` / `std::lcm` from `<numeric>` in C++17.
- `1LL << k` — writing `1 << k` overflows for k ≥ 31.
- `%` on negatives returns a **negative** result in C++/Java (`-7 % 3 == -1`) but a non-negative one in Python (`-7 % 3 == 2`). Normalise with `((x % m) + m) % m`.
- Integer division truncates towards zero in C++/Java, towards −∞ in Python.
