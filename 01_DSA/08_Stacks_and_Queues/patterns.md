# Stacks & Queues — Templates

## 1. Valid parentheses
```python
def is_valid(s):
    pairs = {')': '(', ']': '[', '}': '{'}
    st = []
    for ch in s:
        if ch in pairs:
            if not st or st.pop() != pairs[ch]: return False
        else:
            st.append(ch)
    return not st
```

## 2. Monotonic stack — next greater to the right
```python
def next_greater(a):
    res = [-1] * len(a)
    st = []                                  # indices; values DECREASING
    for i, x in enumerate(a):
        while st and a[st[-1]] < x:
            res[st.pop()] = x
        st.append(i)
    return res
```
Next **smaller**: flip to `a[st[-1]] > x`. **Previous** greater/smaller: after popping, `st[-1]` (if any) is the answer for `i`.

**Circular array** (Next Greater Element II):
```python
n = len(a); res = [-1]*n; st = []
for i in range(2*n):
    x = a[i % n]
    while st and a[st[-1]] < x: res[st.pop()] = x
    if i < n: st.append(i)                   # only push during the first pass
```

## 3. Daily Temperatures
```python
res, st = [0]*len(T), []
for i, t in enumerate(T):
    while st and T[st[-1]] < t:
        j = st.pop(); res[j] = i - j          # distance, hence indices
    st.append(i)
```

## 4. Largest Rectangle in Histogram
```python
def largest_rectangle(h):
    h = h + [0]                                # sentinel flushes the stack
    st, best = [], 0
    for i, x in enumerate(h):
        while st and h[st[-1]] > x:
            height = h[st.pop()]
            width = i - st[-1] - 1 if st else i
            best = max(best, height * width)
        st.append(i)
    return best
```

**Maximal Rectangle** in a binary matrix — run the above per row:
```python
heights = [0] * C
for row in matrix:
    for c in range(C):
        heights[c] = heights[c] + 1 if row[c] == '1' else 0
    best = max(best, largest_rectangle(heights))
```

## 5. Trapping Rain Water — stack version
```python
st, total = [], 0
for i, x in enumerate(h):
    while st and h[st[-1]] < x:
        bottom = h[st.pop()]
        if not st: break
        width = i - st[-1] - 1
        total += (min(h[st[-1]], x) - bottom) * width
    st.append(i)
```

## 6. Monotonic deque — sliding window maximum
```python
from collections import deque
def max_sliding_window(a, k):
    dq, out = deque(), []                      # indices; values DECREASING
    for i, x in enumerate(a):
        while dq and a[dq[-1]] <= x: dq.pop()  # back: smaller can never win
        dq.append(i)
        if dq[0] <= i - k: dq.popleft()        # front: out of window
        if i >= k - 1: out.append(a[dq[0]])
    return out
```

## 7. Min stack — O(1) getMin
```python
class MinStack:
    def __init__(self): self.st = []
    def push(self, x):
        cur_min = x if not self.st else min(x, self.st[-1][1])
        self.st.append((x, cur_min))
    def pop(self):     self.st.pop()
    def top(self):     return self.st[-1][0]
    def getMin(self):  return self.st[-1][1]
```

## 8. Queue from two stacks — amortised O(1)
```python
class MyQueue:
    def __init__(self): self.inb, self.outb = [], []
    def _shift(self):
        if not self.outb:
            while self.inb: self.outb.append(self.inb.pop())
    def push(self, x): self.inb.append(x)
    def pop(self):     self._shift(); return self.outb.pop()
    def peek(self):    self._shift(); return self.outb[-1]
    def empty(self):   return not self.inb and not self.outb
```

## 9. Evaluate Reverse Polish Notation
```python
def eval_rpn(tokens):
    st = []
    for t in tokens:
        if t in '+-*/' and len(t) == 1:
            b = st.pop(); a = st.pop()          # a is the LEFT operand
            if   t == '+': st.append(a + b)
            elif t == '-': st.append(a - b)
            elif t == '*': st.append(a * b)
            else:          st.append(int(a / b))   # truncate toward zero
        else:
            st.append(int(t))
    return st[0]
```

## 10. Decode String — `3[a2[c]]`
```python
def decode(s):
    st, cur, num = [], '', 0
    for ch in s:
        if ch.isdigit(): num = num * 10 + int(ch)
        elif ch == '[':  st.append((cur, num)); cur, num = '', 0
        elif ch == ']':  prev, k = st.pop(); cur = prev + cur * k
        else:            cur += ch
    return cur
```

## 11. Asteroid Collision
```python
st = []
for a in asteroids:
    alive = True
    while alive and a < 0 and st and st[-1] > 0:
        if st[-1] < -a:   st.pop(); continue     # left one explodes
        if st[-1] == -a:  st.pop()
        alive = False
    if alive: st.append(a)
```

## C++ notes
- `stack<int> st;` — `st.top()` then `st.pop()`; `pop()` returns void, unlike Python's.
- `deque<int> dq;` gives `push_back/pop_back/push_front/pop_front` — use it for monotonic deques.
- Prefer `vector<int>` as a stack for speed; `stack` wraps `deque` by default.
- Always check `!st.empty()` before `top()`; on an empty stack it is undefined behaviour, not an exception.
