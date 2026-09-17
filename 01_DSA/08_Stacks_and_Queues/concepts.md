# Stacks & Queues — Concepts

## 1. Core idea in 3 lines
A stack answers "what was the most recent unresolved thing?" and a queue answers "what has been waiting longest?". The **monotonic stack** — a stack whose contents stay sorted — is one of the two or three highest-yield patterns in modern OAs, because it turns a family of O(n²) "look left/right for the first element bigger than me" problems into O(n). The monotonic deque does the same for sliding-window extremes.

---

## 2. Basic stack uses

### Matching and nesting
Valid parentheses, expression evaluation, decoding nested strings, simplifying Unix paths, removing adjacent duplicates. The invariant is always "the stack holds the currently open, unresolved context".

### Expression evaluation
- **Postfix (RPN):** push operands, and on an operator pop two, apply, push back. Watch operand order for `-` and `/` — the *second* pop is the left operand.
- **Infix → postfix (shunting-yard):** operands go straight to output; operators pop from the stack while the top has greater-or-equal precedence, then push.
- **Basic calculator:** handle `+ -` with a running sign and a stack for parentheses; handle `* /` by immediately combining with the previous number.

### Min-stack in O(1)
Either store `(value, min_so_far)` pairs, or keep a second stack of minima pushed only when a new value is ≤ the current minimum (use `≤`, not `<`, or duplicate minima get popped too early).

### Stack ↔ queue simulation
- **Queue from two stacks:** `in` and `out`. Push to `in`; on pop, if `out` is empty, drain `in` into `out`. Amortised O(1), because each element moves between stacks at most once.
- **Stack from two queues:** make either push or pop O(n); the other is O(1).

### Where stacks appear implicitly
The call stack (recursion), iterative DFS, undo history, browser back/forward, and iterative tree traversals.

---

## 3. Monotonic stack — the high-yield pattern

**What it is:** a stack whose values are kept increasing (or decreasing) from bottom to top. Before pushing a new element, pop everything that violates the order. Each element is pushed once and popped once, so the total work is **O(n)** despite the inner `while`.

**What it answers:** for every element, the nearest element to the left or right that is greater or smaller than it.

| Want | Stack order | Scan direction | Pop while |
|---|---|---|---|
| Next greater to the right | decreasing | left → right | `a[stack[-1]] < a[i]` |
| Next smaller to the right | increasing | left → right | `a[stack[-1]] > a[i]` |
| Previous greater to the left | decreasing | left → right | pop, then top is the answer |
| Previous smaller to the left | increasing | left → right | pop, then top is the answer |

**Store indices, not values.** You almost always need the distance (`i − stack[-1]`), and indices let you recover values anyway. Confusing the two is the standard bug in this pattern.

### The two hard applications

**Largest Rectangle in Histogram.** For each bar, the maximal rectangle with that bar as the height extends from the previous smaller bar to the next smaller bar. An increasing stack gives both boundaries in one pass. When a bar is popped at index `i`, its width is `i − stack[-1] − 1` after the pop (or `i` if the stack is empty). Append a sentinel height of 0 so everything is flushed at the end. **Maximal Rectangle** in a binary matrix is this run per row, with heights accumulated.

**Trapping Rain Water.** Water sits between a popped bar and the new taller bar, bounded by `min(left, right) − popped_height` and `right − left − 1` wide. (The two-pointer solution in `03` is simpler and O(1) space — know both, and say why you chose one.)

Other members of the family: Daily Temperatures, Next Greater Element I/II (circular: iterate `2n` times with `i % n`), Stock Span, Sum of Subarray Minimums, Remove K Digits, and "132 pattern".

---

## 4. Monotonic deque — sliding-window extremes

A deque of **indices** whose values are decreasing gives the window maximum in O(1) at the front.

Per element: pop from the back while the back's value is ≤ the new value (they can never be the maximum again, since the new one is larger and stays longer); push the new index; pop from the front if it has fallen out of the window; the front is the answer.

O(n) total, O(k) space. Compare: a heap gives O(n log k) and needs lazy deletion; a multiset gives O(n log k). The deque is the intended answer for Sliding Window Maximum.

---

## 5. Queues

- **BFS** is the dominant use (see `12_Graphs`).
- **Circular buffer / ring queue:** fixed array with head and tail indices mod capacity; the "full vs empty" ambiguity is resolved by keeping a size counter or leaving one slot unused.
- **Deque** supports O(1) at both ends — the basis of the monotonic deque and of 0-1 BFS.
- **Priority queue** is a heap, not a queue (see `10`).

---

## 6. Recall questions

1. Why is a monotonic stack O(n) despite a nested while loop?
2. Which stack order gives "next greater to the right", and in which direction do you scan?
3. Why store indices rather than values?
4. In Largest Rectangle in Histogram, what is the width when a bar is popped?
5. Why append a sentinel 0 to the histogram?
6. How do you implement a queue with two stacks, and what is the amortised cost?
7. Why must a min-stack push on `≤` rather than `<`?
8. Describe the monotonic deque for sliding-window maximum, and its complexity versus a heap.
9. In RPN evaluation, which pop is the left operand?
10. How do you handle "next greater element" in a *circular* array?
