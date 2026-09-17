# Two Pointers & Sliding Window — Templates

## 1. Converging two pointers (sorted)
```python
i, j = 0, len(a) - 1
while i < j:
    s = a[i] + a[j]
    if s == target: return (i, j)
    if s < target:  i += 1
    else:           j -= 1
```

## 2. 3Sum — sort + two pointers
```python
def three_sum(a):
    a.sort(); res = []
    for i in range(len(a) - 2):
        if i and a[i] == a[i-1]: continue          # skip duplicate anchor
        if a[i] > 0: break                          # sorted: no triple can sum to 0
        l, r = i + 1, len(a) - 1
        while l < r:
            s = a[i] + a[l] + a[r]
            if s < 0: l += 1
            elif s > 0: r -= 1
            else:
                res.append([a[i], a[l], a[r]])
                l += 1; r -= 1
                while l < r and a[l] == a[l-1]: l += 1     # skip dup left
                while l < r and a[r] == a[r+1]: r -= 1     # skip dup right
    return res
```

## 3. Container With Most Water
```python
i, j, best = 0, len(h) - 1, 0
while i < j:
    best = max(best, min(h[i], h[j]) * (j - i))
    if h[i] < h[j]: i += 1                          # always move the shorter wall
    else:           j -= 1
```

## 4. Trapping Rain Water — O(1) space
```python
i, j = 0, len(h) - 1
lmax = rmax = total = 0
while i < j:
    if h[i] < h[j]:
        lmax = max(lmax, h[i]); total += lmax - h[i]; i += 1
    else:
        rmax = max(rmax, h[j]); total += rmax - h[j]; j -= 1
```

## 5. Fixed-size window
```python
s = sum(a[:k]); best = s
for i in range(k, len(a)):
    s += a[i] - a[i-k]
    best = max(best, s)
```

## 6. Variable window — LONGEST valid
```python
left = 0; best = 0
cnt = Counter()
for right, x in enumerate(a):
    cnt[x] += 1
    while invalid(cnt):                 # shrink while INVALID
        cnt[a[left]] -= 1
        if cnt[a[left]] == 0: del cnt[a[left]]
        left += 1
    best = max(best, right - left + 1)  # record AFTER the shrink
```

## 7. Variable window — SHORTEST valid
```python
left = 0; best = inf; run = 0
for right, x in enumerate(a):
    run += x
    while run >= target:                # shrink while STILL VALID
        best = min(best, right - left + 1)   # record BEFORE shrinking
        run -= a[left]; left += 1
return 0 if best == inf else best
```

## 8. Longest substring without repeating characters
```python
last = {}; left = 0; best = 0
for right, ch in enumerate(s):
    if ch in last and last[ch] >= left:
        left = last[ch] + 1              # jump, do not step
    last[ch] = right
    best = max(best, right - left + 1)
```

## 9. Minimum window substring — have/need
```python
def min_window(s, t):
    if not t or not s: return ""
    need = Counter(t); missing = len(need)
    left = 0; best = (inf, 0, 0)
    for right, ch in enumerate(s):
        if ch in need:
            need[ch] -= 1
            if need[ch] == 0: missing -= 1
        while missing == 0:                       # valid → try to shrink
            if right - left + 1 < best[0]:
                best = (right - left + 1, left, right)
            lc = s[left]
            if lc in need:
                need[lc] += 1
                if need[lc] > 0: missing += 1
            left += 1
    return "" if best[0] == inf else s[best[1]:best[2]+1]
```

## 10. At-most-K → exactly-K
```python
def at_most(a, k):
    cnt = Counter(); left = res = 0
    for right, x in enumerate(a):
        cnt[x] += 1
        while len(cnt) > k:
            cnt[a[left]] -= 1
            if cnt[a[left]] == 0: del cnt[a[left]]
            left += 1
        res += right - left + 1          # all windows ending at `right`
    return res

def exactly(a, k): return at_most(a, k) - at_most(a, k - 1)
```
`res += right - left + 1` counts every valid subarray ending at `right` — memorise this line, it is the counting idiom for windows.

## 11. Fast/slow pointers
```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow, fast = slow.next, fast.next.next
        if slow is fast: return True
    return False

def cycle_start(head):
    slow = fast = head
    while fast and fast.next:
        slow, fast = slow.next, fast.next.next
        if slow is fast:
            slow = head
            while slow is not fast: slow, fast = slow.next, fast.next
            return slow
    return None
```

## C++ notes
- `unordered_map<char,int>` for window counts; for lowercase-only, `int cnt[26] = {}` is faster.
- Erasing a key when its count hits 0 keeps `cnt.size()` meaningful as the distinct count — otherwise maintain a separate `distinct` counter.
- `string::substr(pos, len)` takes a **length**, not an end index.
