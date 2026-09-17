# Arrays & Strings — Templates

## 1. Prefix sums
```python
pre = [0] * (len(a) + 1)
for i, x in enumerate(a): pre[i+1] = pre[i] + x
rng = pre[r+1] - pre[l]                   # sum of a[l..r] inclusive
```

**2-D prefix sum**
```python
S = [[0]*(C+1) for _ in range(R+1)]
for r in range(R):
    for c in range(C):
        S[r+1][c+1] = S[r][c+1] + S[r+1][c] - S[r][c] + g[r][c]
# sum of the rectangle (r1,c1)..(r2,c2) inclusive:
tot = S[r2+1][c2+1] - S[r1][c2+1] - S[r2+1][c1] + S[r1][c1]
```

## 2. Difference array — O(1) range update
```python
diff = [0] * (n + 1)
for l, r, v in updates:
    diff[l] += v
    diff[r+1] -= v
a, run = [], 0
for i in range(n):
    run += diff[i]
    a.append(run)
```

## 3. Kadane
```python
def max_subarray(a):
    best = cur = a[0]                      # start from a[0], NOT 0
    for x in a[1:]:
        cur = max(x, cur + x)
        best = max(best, cur)
    return best
```

**Maximum product subarray**
```python
def max_product(a):
    best = hi = lo = a[0]
    for x in a[1:]:
        if x < 0: hi, lo = lo, hi          # a negative swaps the roles
        hi = max(x, hi * x)
        lo = min(x, lo * x)
        best = max(best, hi)
    return best
```

## 4. Rotate by k in O(1) space
```python
def rotate(a, k):
    n = len(a); k %= n
    def rev(i, j):
        while i < j: a[i], a[j] = a[j], a[i]; i += 1; j -= 1
    rev(0, n-1); rev(0, k-1); rev(k, n-1)
```

## 5. Dutch national flag (Sort Colors)
```python
low, mid, high = 0, 0, len(a) - 1
while mid <= high:
    if a[mid] == 0:
        a[low], a[mid] = a[mid], a[low]; low += 1; mid += 1
    elif a[mid] == 1:
        mid += 1
    else:
        a[mid], a[high] = a[high], a[mid]; high -= 1   # do NOT advance mid
```

## 6. Two-pointer overwrite (remove in place)
```python
write = 0
for read in range(len(a)):
    if keep(a[read]):
        a[write] = a[read]; write += 1
return write                                # new length
```

## 7. Product of array except self — O(1) extra space
```python
res = [1] * n
left = 1
for i in range(n):     res[i] = left;  left  *= a[i]
right = 1
for i in range(n-1, -1, -1): res[i] *= right; right *= a[i]
```

## 8. Matrix rotate 90° clockwise, in place
```python
n = len(m)
for r in range(n):                          # transpose
    for c in range(r+1, n):
        m[r][c], m[c][r] = m[c][r], m[r][c]
for row in m: row.reverse()                 # reverse each row
```

## 9. Spiral traversal
```python
top, bot, left, right = 0, R-1, 0, C-1
out = []
while top <= bot and left <= right:
    for c in range(left, right+1): out.append(g[top][c])
    top += 1
    for r in range(top, bot+1):    out.append(g[r][right])
    right -= 1
    if top <= bot:                                       # guard
        for c in range(right, left-1, -1): out.append(g[bot][c])
        bot -= 1
    if left <= right:                                    # guard
        for r in range(bot, top-1, -1): out.append(g[r][left])
        left += 1
```

## 10. Expand around centre — longest palindromic substring
```python
def longest_pal(s):
    best = ""
    def expand(l, r):
        while l >= 0 and r < len(s) and s[l] == s[r]: l -= 1; r += 1
        return s[l+1:r]
    for i in range(len(s)):
        for cand in (expand(i, i), expand(i, i+1)):       # odd and even
            if len(cand) > len(best): best = cand
    return best
```

## 11. Frequency array for lowercase strings
```python
cnt = [0] * 26
for ch in s: cnt[ord(ch) - 97] += 1
```

## 12. KMP prefix function
```python
def prefix_function(s):
    pi = [0] * len(s)
    for i in range(1, len(s)):
        j = pi[i-1]
        while j and s[i] != s[j]: j = pi[j-1]
        if s[i] == s[j]: j += 1
        pi[i] = j
    return pi

def kmp_search(text, pat):
    pi = prefix_function(pat + '\x00' + text)
    m = len(pat)
    return [i - 2*m for i, v in enumerate(pi) if v == m]
```

## C++ notes
- `vector<int> pre(n+1, 0);` — `std::partial_sum` is available but a manual loop is clearer.
- `reverse(a.begin()+i, a.begin()+j+1)` for the rotation trick; `rotate(a.begin(), a.begin()+k, a.end())` does it directly.
- `std::string` is mutable, so `s += c` in a loop is fine (amortised O(1)); `s.substr()` still copies.
- `swap(a[i], a[j])` rather than a temporary.
