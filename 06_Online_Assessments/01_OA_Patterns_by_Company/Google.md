
# Google — Online Assessment and Coding Rounds

> **Accuracy note.** OA formats change every recruiting season, and companies run different
> papers for different campuses, roles and dates. Treat everything below as the *shape* of the
> test — what it measures and how to prepare — and verify the exact duration, section count and
> cutoff against this year's campus notice and last year's seniors before you sit the test.


---

## 1. How Google actually recruits on campus

Google uses fewer, harder filters than the volume recruiters.

```
Application / referral / campus shortlist
        │
        ▼
 Online Assessment  (2 problems, ~90 min)   — for campus and intern tracks
        │
        ▼
 Technical phone screen(s)  (1-2 x 45 min, live coding in a shared doc)
        │
        ▼
 Onsite / virtual loop  (4-5 rounds: 3 coding, 1 behavioural ["Googleyness"],
                         1 system design for experienced roles)
        │
        ▼
 Hiring committee → team match → offer
```

Related but separate funnels: **Kick Start** (now retired but its archive is the single best
practice set for Google-style problems), **Code Jam**, **Hash Code**, and **STEP** internships.

---

## 2. OA structure

| Item | Typical |
|---|---|
| Platform | Google's own assessment portal (`hiringassessment.google.com` style) |
| Duration | 60-90 minutes |
| Problems | **2 coding problems** |
| Scoring | Hidden tests; correctness plus efficiency; partial credit possible |
| Languages | C++, Java, Python, Go, JavaScript, Kotlin |
| Distinctive | ⭐ Harder than Amazon/Microsoft OAs — expect **Medium-to-Hard** |

Some campus rounds also use an **in-house judge with sample + hidden tests**, and a few use a
Kick-Start-style format with multiple test sets per problem (a small-input set worth partial
points and a large-input set requiring the efficient solution).

---

## 3. What makes a Google problem different ⭐⭐⭐

Google problems are usually **not** a named algorithm in disguise. They are:

1. **Heavy on observation.** The intended solution usually rests on one non-obvious property of the
   problem ("notice that the answer is monotone in k", "notice that you only ever need the last two
   states", "notice the parity is invariant"). Finding that property *is* the problem.
2. **Constraint-driven.** The constraints tell you the intended complexity. Read them first.

   | Constraint | Intended complexity |
   |---|---|
   | `n ≤ 10` | Brute force / permutations `O(n!)` |
   | `n ≤ 20-25` | Bitmask DP / meet in the middle `O(2^n)` |
   | `n ≤ 100-500` | `O(n³)` — Floyd-Warshall, interval DP |
   | `n ≤ 5000` | `O(n²)` — 2-D DP, all-pairs on small graphs |
   | `n ≤ 10⁵-10⁶` | `O(n log n)` — sort, heap, binary search, segment tree |
   | `n ≤ 10⁷-10⁸` | `O(n)` or `O(n log log n)` — two pointers, sieve, prefix sums |
   | `n ≤ 10¹⁸` | `O(log n)` — binary search on answer, maths, matrix exponentiation |

   ⭐ **This table is the most useful thing on this page.** Reading the constraint before the
   statement tells you which family of solutions to even consider.
3. **Mathematically flavoured.** Number theory, combinatorics, modular arithmetic and probability
   appear far more often than at other companies.
4. **Edge cases matter.** The hidden tests include the degenerate cases deliberately.

---

## 4. High-frequency topics

| Tier | Topic | Notes |
|---|---|---|
| **Core** | **Dynamic programming** ⭐⭐⭐ | 1-D, 2-D, on trees, on subsets (bitmask), digit DP. The most Google-ish topic |
| **Core** | **Graphs** | BFS/DFS, shortest path (Dijkstra, 0-1 BFS), topological sort, union-find, MST, bipartite check |
| **Core** | **Binary search on the answer** ⭐ | "Minimise the maximum", "maximise the minimum" — extremely common |
| **Core** | **Greedy with an exchange argument** | Must be able to *justify* the greedy choice |
| High | **Combinatorics and number theory** | nCr mod p, sieve, gcd/lcm, modular inverse, inclusion-exclusion |
| High | **Trees** | LCA, diameter, rerooting, subtree aggregates |
| High | **Two pointers / sliding window** | Often as one step inside a bigger problem |
| High | **Prefix sums and difference arrays** | 1-D and 2-D; range update tricks |
| Medium | **Segment tree / BIT** | Range query + point update; rarer in OA but appears in Kick Start |
| Medium | **Strings** | KMP/Z-algorithm, hashing, trie |
| Medium | **Geometry** | Convex hull, sweep line; rare but non-zero |
| Medium | **Bit manipulation** | Subsets, XOR tricks, bit DP |

### Recurring problem shapes
- *"You can do operation X at most k times; maximise/minimise Y."* → binary search on the answer,
  or DP with `k` as a dimension.
- *"Count the number of ways / subsequences / paths, modulo 10⁹+7."* → combinatorics or DP.
- *"Given a grid and a rule, find the minimum cost to reach the end."* → BFS/Dijkstra/DP.
- *"Partition the array so that some quantity is balanced."* → binary search or DP.
- *"Find the k-th smallest/largest something."* → heap, binary search on value, or order statistics.

---

## 5. How to attack a Google OA problem ⭐⭐

```
 1. Read the CONSTRAINTS first (30 s)           → narrows the solution family immediately
 2. Read the statement twice (2 min)            → write down what is being asked in one sentence
 3. Work the given examples BY HAND (3 min)     → this is where the key observation appears
 4. Construct your own tiny example (2 min)     → n = 1, n = 2, all-equal, extremes
 5. State a brute force and its complexity      → always have a fallback you can code
 6. Look for the property that collapses it     → monotonicity, invariant, optimal substructure,
                                                   exchange argument, symmetry
 7. Code it, then test the degenerate cases
```

> **Do not start coding before step 4.** On a Google problem, twenty minutes of coding the wrong
> idea is a lost problem; five minutes of hand-working the examples usually reveals the idea.

If you cannot find the intended solution with 25 minutes left, **code the brute force**. Partial
credit is real and a submitted brute force is worth more than an unfinished optimal solution.

---

## 6. Beyond the OA — what the interviews add

| Round | What is tested |
|---|---|
| Coding x3 | The same problem families, but *live*: you must think aloud, take hints, and write compiling code in a plain document with no autocomplete ⚠️ |
| Googleyness & Leadership | Collaboration, ambiguity, bias for action, dealing with conflict. STAR answers |
| System design (experienced) | Scale, storage, consistency, sharding, caching — see `04_System_Design/` |

**Live-coding-specific skills worth practising now:**
- Narrate your reasoning continuously; silence is scored badly.
- Ask clarifying questions about input size, duplicates, and invalid input before coding.
- State the complexity before and after coding.
- Write in a plain text editor (Google Docs, `coderpad`) with no syntax highlighting at least five
  times before the interview. It is startlingly harder than an IDE.

---

## 7. Preparation plan (8 weeks — this one needs more time)

| Week | Focus |
|---|---|
| 1 | Complexity intuition + the constraints table; prefix sums, two pointers, sorting/greedy |
| 2 | Binary search on the answer — 15 problems until it is automatic |
| 3 | Graphs: BFS/DFS/Dijkstra/topological sort/union-find |
| 4 | DP part 1: 1-D, knapsack family, LIS, coin change, interval DP |
| 5 | DP part 2: 2-D, tree DP, bitmask DP, digit DP |
| 6 | Number theory + combinatorics with modular arithmetic |
| 7 | **Kick Start archive rounds**, timed, 3 hours each — the closest available proxy |
| 8 | Two full 90-minute 2-problem mocks + live-coding practice in a plain document |

---

## 8. Practice sources worth the time

- **Google Kick Start archive** (Rounds A-H across years) — the single best proxy for the OA style.
- **Codeforces Div 2 A-C** — trains the observation muscle better than LeetCode does.
- **LeetCode Google-tagged Hard** — for the named-algorithm coverage.
- **AtCoder Beginner Contest D-F** — excellent for DP and maths.

---

## 9. Test-day checklist

- [ ] Constraints first, always
- [ ] Hand-work every provided example before coding
- [ ] Keep a brute force in reserve; code it if 25 minutes remain and you have no idea
- [ ] Check `long long` overflow, modular arithmetic, and `n = 0/1` edge cases
- [ ] Reserve five minutes at the end to re-run both problems on degenerate inputs
