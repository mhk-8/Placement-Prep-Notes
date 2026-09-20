
# Microsoft — Online Assessment

> **Accuracy note.** OA formats change every recruiting season, and companies run different
> papers for different campuses, roles and dates. Treat everything below as the *shape* of the
> test — what it measures and how to prepare — and verify the exact duration, section count and
> cutoff against this year's campus notice and last year's seniors before you sit the test. The
> topic lists and strategy age far more slowly than the timings do.


---

## 1. The funnel

```
Campus shortlist / referral
        │
        ▼
 OA  (Codility-style, 2-3 coding problems, ~90 min)     ← this document
        │
        ▼
 Group Fly / Written round    (on some campuses: hand-written code on paper) ⚠️
        │
        ▼
 Technical rounds x2-3   (DSA + CS fundamentals + project)
        │
        ▼
 AA round ("As Appropriate" — senior/hiring manager, design + behaviour)
        │
        ▼
 Offer
```

⚠️ On several Indian campuses Microsoft runs a **Group Fly**: a paper round where you hand-write
complete, compilable code for 1-2 problems in 30-40 minutes. Practise writing code **on paper** at
least twice — it is a genuinely different skill (no autocomplete, no compiler, no backspace).

---

## 2. OA structure

| Item | Typical value |
|---|---|
| Platform | **Codility** most often; sometimes HackerRank or Microsoft's in-house portal |
| Duration | 60-90 minutes (occasionally 120) |
| Problems | **2-3 coding problems**, no MCQs in the standard campus OA |
| Scoring | Correctness **and performance** scored separately by Codility ⭐ |
| Languages | C, C++, C#, Java, Python, JavaScript, Go and others |
| Proctoring | Full-screen enforcement, tab-switch logging; webcam on some variants |

### The Codility scoring model ⚠️⭐⭐
This is the thing candidates most often misunderstand.

```
Task score = (Correctness score + Performance score) / 2
   Correctness : hidden functional tests, including edge cases
   Performance : large-input tests with a hard time limit
```

Consequences:
- A correct `O(n²)` solution where `O(n log n)` was intended typically scores **~50-60%**, not 0.
  So **always submit something correct**.
- Edge cases are weighted heavily: empty array, single element, all-equal elements, maximum
  constraint values, negative numbers, integer overflow.
- Codility gives you a **"test"** button with example tests plus your own custom input. Use it. Run
  at least: empty, size 1, all identical, and the maximum-size case before final submit.

> **Codility habit: write the edge cases into your own test input before you optimise.** Most lost
> marks at Microsoft are edge-case marks, not algorithm marks.

---

## 3. High-frequency topics

Microsoft leans more toward **arrays, strings, and clean implementation** than toward heavy graph
theory. It also asks more **"simulate this precisely"** problems than Amazon does.

| Rank | Topic | Notes and typical problems |
|---|---|---|
| 1 | **String manipulation** | Parse, transform, compress, validate; "remove adjacent duplicates"; "longest palindromic substring"; format conversion |
| 2 | **Arrays with a constraint twist** | Max/min after k operations, rotate, rearrange, missing/duplicate number ⭐ |
| 3 | **Hash map + frequency** | Anagrams, pair sums, most-common element with tie-breaking |
| 4 | **Two pointers / sliding window** | Container with most water, longest substring with k distinct |
| 5 | **Linked lists** ⭐ | Microsoft asks these more than most: reverse in k-groups, detect and remove cycle, merge, reorder, LRU cache |
| 6 | **Trees and BST** | Validate BST, LCA, level order, right side view, convert sorted array to BST |
| 7 | **Greedy with proof obligation** | Task assignment, interval selection, minimum operations |
| 8 | **DP (1-D, and classic 2-D)** | Edit distance, LCS, coin change, maximum subarray variants |
| 9 | **Stacks** | Valid parentheses variants, expression evaluation, next greater |
| 10 | **Matrix simulation** | Spiral order, rotate image, set matrix zeroes, game of life |
| 11 | **Bit manipulation** | Single number, count set bits, power of two, swap without temp |
| 12 | **Graphs** | Present but lighter than Amazon; BFS shortest path, course schedule |

### The Microsoft signature: precise simulation
Problems like "a robot moves according to these rules for N steps, report the final state" or
"apply this text-formatting specification exactly" appear regularly. They have **no clever
algorithm** — they test whether you can read a long specification carefully and implement it
without an off-by-one.

> **Strategy for simulation problems:** re-read the statement twice, write the rules as a numbered
> list in comments, then implement one rule per line. Do not start coding from memory of the first
> read.

---

## 4. CS fundamentals in the OA

Some Microsoft OA variants (especially for non-SDE or internship tracks) add an MCQ section on:
- Operating systems: processes vs threads, deadlock, scheduling, virtual memory
- Databases: normalisation, joins, indexing, transactions and ACID
- Networks: TCP vs UDP, the OSI layers, HTTP/HTTPS, DNS
- OOP: inheritance vs composition, virtual functions, abstract classes, SOLID
- C/C++ output prediction: pointers, memory, static, operator precedence

`05_MCQ_Core_CS_Banks/` in this folder covers all of these.

---

## 5. Group Fly / written round (campus-specific)

Format: 1-2 problems, 30-45 minutes, **hand-written full programs** on paper, collected and graded.

How it is marked (approximately):
- Correct approach: ~40%
- Complete, compilable-looking code with declarations and includes: ~30%
- Edge-case handling: ~20%
- Readability and complexity annotation: ~10%

Practical advice:
- Write the function signature, then the approach in two lines, then the code.
- **State the time and space complexity at the bottom** — graders look for it.
- Handle `null` / empty input explicitly, even in one line.
- Leave a blank line between logical blocks; write legibly. Illegible code scores zero.
- Do not scribble out large blocks; use a single line strike-through.

---

## 6. Preparation plan

| Weeks out | Focus |
|---|---|
| 4 | Arrays, strings, linked lists — Microsoft's core. 5 problems/day |
| 3 | Trees, stacks, DP classics. Start solving **on Codility's own free demo tasks** to get used to the interface |
| 2 | Timed 90-minute 3-problem sets; force yourself to write the four edge-case tests before submitting |
| 1 | One paper-coding session; CS fundamentals MCQ revision; LRU cache and reverse-in-k-groups from memory |

---

## 7. Test-day checklist

- [ ] Open Codility's demo task once beforehand so the UI is familiar
- [ ] Read all problems first; rank by confidence
- [ ] For each: brute force mentally → intended complexity → *then* code
- [ ] Run custom tests: empty, single, all-equal, maximum size, negatives
- [ ] Check integer overflow (`int` vs `long`) — a classic Codility performance-test failure
- [ ] Submit every task, even partially correct ones
- [ ] Annotate complexity in a comment; it costs nothing
