# Stacks & Queues — Pitfalls

## Monotonic stack
- **Storing values instead of indices.** You then cannot compute distances or widths. Store indices and index into the array.
- Getting the pop condition's direction backwards — the stack must be *decreasing* for "next greater" and *increasing* for "next smaller".
- Using `<` where `<=` is needed (or vice versa) when duplicates exist. For Largest Rectangle either works if handled consistently; for "sum of subarray minimums" the strictness decides whether equal elements are double-counted.
- Forgetting the sentinel, so elements left on the stack at the end are never processed.
- Width formula: it is `i - st[-1] - 1` **after** popping, and `i` when the stack becomes empty. Computing it before the pop is the classic error.
- Claiming O(n²) because of the nested while — state the amortised argument: each index is pushed once and popped once.

## Monotonic deque
- Popping from the front before appending, so a just-added index can be discarded.
- Using `<` instead of `<=` on the back pop, leaving stale equal values that inflate the window.
- Forgetting the `if i >= k - 1` guard, emitting answers before the first full window.
- Comparing `dq[0] <= i - k` versus `< i - k + 1` inconsistently; write it once and keep it.

## Basic stack use
- `st.pop()` on an empty stack. In C++ this is undefined behaviour, not an exception.
- In RPN, popping the operands in the wrong order for `-` and `/`.
- Integer division direction: Python's `//` floors (so `-7 // 2 == -4`); most problems want truncation toward zero — use `int(a / b)`.
- Min-stack pushing only on strict `<`, so duplicate minima are popped too early and `getMin` becomes wrong.
- Not returning `not st` at the end of a parentheses check — unclosed brackets pass silently.

## Queues
- Circular buffer: not distinguishing full from empty when head == tail. Keep a size counter.
- Using `list.pop(0)` in Python as a queue — that is O(n) per operation and turns BFS into O(n²). Use `collections.deque`.
- In "stack from two queues", forgetting that one of push or pop must be O(n).

## General
- Choosing a stack when a simple counter suffices (e.g. matching only one bracket type).
- Recursion where an explicit stack is required by depth limits.
- Mutating the input array (appending the sentinel) when the caller still needs it.
