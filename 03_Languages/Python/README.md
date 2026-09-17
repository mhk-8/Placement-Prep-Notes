# Python for Interviews

> Folder: `03_Languages/Python`

## Why this matters
Fastest to write, best for ML roles. Risky for CPU-bound OA problems with tight limits.

## Must-know checklist
- [ ] collections: defaultdict, Counter, deque, OrderedDict
- [ ] heapq (min-heap only — negate for max)
- [ ] bisect for binary search
- [ ] itertools: permutations, combinations, product, accumulate
- [ ] List/dict/set comprehensions; slicing semantics
- [ ] sys.setrecursionlimit; sys.stdin.readline for fast I/O
- [ ] Mutable default arguments; shallow vs deep copy
- [ ] GIL, generators, decorators (for interviews)

## Online-assessment angle
Python can TLE on O(n log n) with n=1e6. Know when to switch to C++ mid-OA.

## Interview angle
For ML roles expect Python depth: GIL, memory model, numpy vectorisation, pandas internals.

## Suggested files in this folder
- `syntax-reference.md`
- `library-complexities.md`
- `gotchas.md`
- `snippets.md`

---
Revision rule: after every session, move anything you got wrong into `/10_Mistake_Log_and_Revision/`.
