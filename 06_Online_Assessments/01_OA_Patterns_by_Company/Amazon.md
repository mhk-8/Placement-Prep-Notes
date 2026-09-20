
# Amazon — Online Assessment

> **Accuracy note.** OA formats change every recruiting season, and companies run different
> papers for different campuses, roles and dates. Treat everything below as the *shape* of the
> test — what it measures and how to prepare — and verify the exact duration, section count and
> cutoff against this year's campus notice and last year's seniors before you sit the test. The
> topic lists and strategy age far more slowly than the timings do.


---

## 1. Where the OA sits in the funnel

```
Application / campus shortlist
        │
        ▼
 OA 1  (Debugging + Coding + Work Style + Work Simulation)      ← this document
        │
        ▼
 Phone / Online technical round(s)          (1-2 rounds, DSA + behaviour)
        │
        ▼
 Loop: 3-5 rounds  (DSA x2, LLD/HLD, Bar Raiser)
        │
        ▼
 Offer
```

Amazon's OA is unusual in two ways: it has **non-coding sections that are actually scored**, and
the coding section is **fully auto-evaluated with partial credit and code-quality signals**.

---

## 2. Structure (SDE-1 / campus variant)

| # | Section | Typical time | What it is |
|---|---|---|---|
| 1 | **Code Debugging** | 20 min for ~6-7 snippets | Short buggy programs; find and fix. Often optional/variant-dependent |
| 2 | **Coding (DSA)** | **70-105 min for 2 problems** | The section that decides the shortlist |
| 3 | **Work Style Assessment** | 10-15 min | Forced-choice statements mapped to the Leadership Principles |
| 4 | **Work Simulation** | 20-45 min | A "day in the life" inbox: emails, dashboards, chat, choose responses |
| 5 | Logical / Reasoning (some variants) | 20-35 min | Figure series, arrangements, basic quant |

**Platform:** Amazon's own assessment portal (`amazon.jobs` / hire.amazon), historically built on a
HackerRank-style engine. Expect a proctored, full-screen, webcam-on environment with tab-switch
detection.

**Language support:** C, C++, Java, Python, C#, JavaScript, Kotlin, Swift and more. **Choose the
language you are fastest in, not the fastest language** — Amazon's limits are generous enough that
Python rarely TLEs on intended solutions.

---

## 3. The coding section in detail

### Difficulty band
Squarely **LeetCode Medium**, occasionally an easy-medium plus a hard-medium pair. Amazon does
*not* usually ask exotic algorithms; it asks well-known patterns with a business-flavoured story
wrapper ("packages", "servers", "fulfilment centres", "prime subscribers").

### The single most important mechanic: **partial scoring** ⚠️
Each problem has a hidden test suite. You are scored on **tests passed**, not on
all-or-nothing submission.

> **Rule: submit a correct brute force early.** A brute force that passes 40% of tests beats an
> elegant solution you never finished. Write brute force → submit → then optimise → submit again.
> Your best submission counts.

### Code quality signals
Amazon's grader also looks at compilation warnings, unreachable code and, in some variants, a
"code quality" score. Practical implications:
- Remove debug prints before final submit.
- Use meaningful variable names — it costs 5 seconds and can only help.
- Do not leave commented-out dead blocks.

### High-frequency topics (ranked by observed frequency)

| Rank | Topic | Representative problem shapes |
|---|---|---|
| 1 | **Hash map + counting** | Group items, first unique, k-frequent, anagram families, "count pairs with property X" |
| 2 | **Two pointers / sliding window** | Longest substring without repeats, min window, max sum subarray of size k, fruit-into-baskets |
| 3 | **Heaps / top-K** | K closest points to origin ⭐ (an Amazon classic), K most frequent, merge K sorted lists, task scheduling |
| 4 | **Graph BFS/DFS on a grid** | Number of islands, rotting oranges ⭐, shortest path in binary matrix, flood fill, maze with obstacles |
| 5 | **Sorting + greedy with an interval twist** | Merge intervals, meeting rooms, minimum platforms, assign tasks to workers |
| 6 | **Binary search on the answer** ⭐ | Minimum capacity to ship packages in D days ⭐⭐, split array largest sum, Koko eating bananas |
| 7 | **Trees** | Level order, diameter, LCA, path sum, serialize/deserialize (rarer in OA) |
| 8 | **DP (1-D and simple 2-D)** | Climbing stairs variants, house robber, coin change, longest common subsequence, knapsack-lite |
| 9 | **Strings** | Parsing log lines, palindromes, compression, string transformations |
| 10 | **Union-Find** | Connected components, number of provinces, accounts merge |
| 11 | **Trie** | Prefix search, autocomplete, "search suggestions system" ⭐ |
| 12 | **Monotonic stack** | Next greater element, largest rectangle, daily temperatures |

⭐ **"Minimum capacity to ship packages within D days"** and **"K closest points to origin"** are
so frequently reported that you should be able to write both from memory in under six minutes.

### Recurring Amazon-flavoured problem families
- **Fulfilment / warehouse**: pack items, assign orders, minimise trips → greedy or binary search.
- **Server load / cluster**: distribute load, find peak, schedule tasks → heap or prefix sums.
- **Product search / suggestions**: prefix matching → trie or sorted array + binary search.
- **Review / rating**: top-K, running median, weighted average → heap.
- **Transaction / order log parsing**: string parsing + hash map.
- **Shopping-cart pricing**: greedy plus a discount rule, sometimes DP.

---

## 4. The Debugging section

6-7 snippets, ~2-3 minutes each, in the language you select. Bugs are deliberately shallow:

| Bug family | What to look for |
|---|---|
| Off-by-one | `<=` vs `<` in loop bounds, `n` vs `n-1` |
| Wrong operator | `=` vs `==`, `&` vs `&&`, `+` vs `-` |
| Uninitialised / wrongly initialised | `max = 0` when values can be negative (should be `INT_MIN`) |
| Wrong variable used | `i` where `j` was meant — very common in nested loops |
| Missing return / wrong return placement | Return inside the loop instead of after |
| Integer division | `(a + b) / 2` overflow, or `int` division where `double` was intended |
| Off-by-one on string index | `s[i+1]` at the last index |
| Inverted condition | `if (x > y)` where `<` was meant |

> **Strategy:** do not read the snippet like prose. Look at the loop bounds, the comparison
> operators and the initialisation first — that covers roughly 80% of planted bugs. Budget
> 2 minutes; if nothing jumps out, flag it and move on.

---

## 5. Work Style Assessment (Leadership Principles)

You are shown pairs or sliders of statements such as "I prefer to gather all the data before
deciding" vs "I move quickly with the information available" and choose which describes you.

**It is scored.** It maps onto the 16 Leadership Principles. Read them once before the test:

| Principle | The behaviour the test is looking for |
|---|---|
| Customer Obsession | Start from the customer, work backwards |
| Ownership | Act beyond your job description; think long-term; never say "not my job" |
| Invent and Simplify | Seek new solutions; simplify |
| Are Right, A Lot | Strong judgement; seek diverse perspectives |
| Learn and Be Curious | Always improving |
| Hire and Develop the Best | Raise the bar, coach others |
| Insist on the Highest Standards | Relentlessly high bar; fix root causes |
| Think Big | Bold direction |
| **Bias for Action** | **Speed matters; calculated risk-taking; many decisions are reversible** ⭐ |
| Frugality | Do more with less |
| Earn Trust | Listen, speak candidly, be self-critical |
| Dive Deep | Stay connected to the details; audit frequently |
| Have Backbone; Disagree and Commit | Challenge respectfully, then commit fully |
| Deliver Results | Focus on key inputs; deliver with quality, on time |
| Strive to be Earth's Best Employer | Empathy, growth, safety |
| Success and Scale Bring Broad Responsibility | Think about consequences |

> **Do not "game" it into a caricature.** The instrument has consistency checks: if you answer
> every item as the maximum of every trait, you look inconsistent. Answer honestly but *lean*
> toward Ownership, Bias for Action, Dive Deep and Customer Obsession where the choice is genuinely
> close.

---

## 6. Work Simulation

A simulated inbox over ~20-45 minutes: emails from a manager, a peer, a metrics dashboard, a chat
thread. You choose the "most effective" and "least effective" responses.

Heuristics that score well:
1. **Look at the data before acting** — pick the option that checks the dashboard or the logs.
2. **Communicate proactively** — inform the affected stakeholder rather than staying silent.
3. **Fix the root cause**, do not just patch the symptom or escalate immediately.
4. **Prefer the customer-impacting fix** over the internally convenient one.
5. **Do not throw a colleague under the bus** and do not silently take over their work; talk to them.
6. Escalation is right only after you have gathered facts and attempted a fix.

---

## 7. Preparation plan

| Weeks out | What to do |
|---|---|
| 4 | Drill the 12 topic families above; 4-5 problems/day from LeetCode Amazon-tagged Medium |
| 3 | Add timed 2-problem sets in 70 minutes; practise writing brute force first, then optimising |
| 2 | Debugging drills: take your own old solutions, introduce bugs, fix them fast |
| 1 | Read the Leadership Principles; do one full mock under proctored conditions; revise templates |
| Day before | Sleep. Re-read your own template file. Nothing new. |

**Template to have memorised** (fast I/O, common imports, a `main` that reads the exact input
format the platform gives you — Amazon usually provides a `solve()` stub, so practise filling a
stub rather than writing `main` from scratch).

---

## 8. Test-day checklist

- [ ] Stable internet; close every other tab and application (tab switching is logged)
- [ ] Webcam clear, room lit, ID ready
- [ ] Read **both** coding problems in the first 3 minutes before writing anything
- [ ] Start with the one you can definitely finish
- [ ] Write brute force → **submit** → optimise → submit
- [ ] Reserve the last 5 minutes to re-submit your best version and remove debug prints
- [ ] Do not leave the Work Style section blank — it is scored

---

## 9. After the test

Write down, within one hour: both problem statements, your approach, what you submitted, what you
missed. Amazon interviewers sometimes revisit your OA solution, and more importantly the same
problem families recur across seasons. File it in `/10_Mistake_Log_and_Revision/`.
