# Choosing Your Primary OA Language

> Decide once, in Week 0. Freeze it by 18 October. This file exists so the decision is made calmly rather than at 2 a.m. the night before an OA.

---

## 1. The honest comparison

| | C++ | Python | Java |
|---|---|---|---|
| Execution speed | **fastest** (~1×) | 30–100× slower | ~1.5–2× slower than C++ |
| Lines to write a solution | most | **fewest** | most (verbose) |
| Standard library for contests | **richest** | very good | good |
| Compile-time error catching | strong | none | **strongest** |
| Risk of silent wrong answers | overflow, UB | none from overflow | overflow |
| Recursion depth | ~10⁵–10⁶ | **1000 by default** | ~10⁴–10⁵ |
| Debugging on a bare OA platform | hard | easy | medium |
| ML / data roles | rarely used | **required** | rarely |
| Typing speed cost | high | **low** | highest |

## 2. The decision, in one table

| If your situation is… | Choose |
|---|---|
| Targeting SDE roles at product companies, tight time limits | **C++** |
| Targeting AI/ML or data roles | **Python** (you need it anyway) |
| You already write one of them comfortably | **the one you already write** |
| Your OA platform restricts languages | check `08_Company_Wise/<Company>/01-oa-pattern.md` first |
| You are genuinely equally comfortable | **C++ for SDE, Python for ML** |

**The strongest argument is not speed — it is your existing fluency.** A candidate who writes Python at 40 lines a minute beats one who writes C++ at 15, on every problem that Python can pass.

## 3. When Python actually fails

Python is 30–100× slower than C++. That matters only when the intended complexity is already near the limit:

| Situation | Python verdict |
|---|---|
| n ≤ 10⁵, O(n log n) intended | fine |
| n ≤ 10⁶, O(n) intended | usually fine with fast I/O |
| n ≤ 10⁶, O(n log n) intended | **borderline** — may TLE |
| n ≤ 10⁷ | **will TLE** |
| Heavy nested loops, tight constants | **will TLE** |
| Deep recursion (10⁵ frames) | needs `setrecursionlimit`, still risky |

**Mitigations that work:** `sys.stdin.readline` instead of `input()`, `''.join()` instead of `+=`, list comprehensions instead of loops, `collections`/`heapq`/`bisect` instead of hand-rolled structures, and avoiding function-call overhead in the innermost loop.

**Mitigations that do not work:** micro-optimising a fundamentally quadratic solution.

## 4. The two-language strategy

Many strong candidates keep a **primary** and a **fallback**:

- **Primary: Python.** Write everything here first. It is fast to type and hard to get syntactically wrong.
- **Fallback: C++.** If a solution TLEs and you are confident the algorithm is right, port it. A correct algorithm ports in a few minutes because the structure is already decided.

This only works if you are genuinely fluent in both. If you are not, one language you know well beats two you know badly — and under exam pressure, the second language is where the compile errors happen.

## 5. Whatever you choose, own these five things

1. **Fast I/O**, typed from memory in under 20 seconds
2. **Every container** and its complexity (`library-complexities.md`)
3. **Custom comparators** — three ways
4. **Overflow / recursion-depth / division-rounding** behaviour
5. **A starter template file** you begin every problem from

## 6. My decision

> Fill this in during Week 0 and do not revisit it before Gate A.

**Primary OA language: ______________________**
**Fallback (if any): ______________________**
**Decided on: ______________** · **Frozen until: 18 Oct 2026**

**Why:** ______________________________________________

**The one thing I most need to drill in it:** ______________________
