
# Night Before — The Only File You Read

> **This is the last thing you read before an OA or an interview. Nothing else.** ⭐
> Two pages. No new topics. If something on this page feels unfamiliar, that is information
> about tomorrow's risk, not an instruction to go and study it now. ⚠️
>
> **Stop by 22:30. Sleep is a performance input, not a luxury.**

---

## 1. Logistics — check these first, then put the laptop away ⭐

```
□ Time, platform and joining link confirmed; link opened once to check it loads
□ Laptop charged + charger packed; phone charged
□ Internet backup (mobile hotspot tested, not assumed) ⚠️
□ ID card / college ID, resume printouts (3 copies)
□ Camera and microphone tested on the actual platform
□ Quiet room booked, door closed, notifications off, browser tabs closed
□ Water on the desk, bathroom done 15 minutes before
□ Pen and rough paper (check whether the platform allows it)
□ Alarm set, with a backup alarm
```

---

## 2. Constraints → complexity ⭐⭐⭐ (the single highest-value table)

| n ≤ | Target | Technique |
|---|---|---|
| 20 | O(2ⁿ) | Bitmask |
| 500 | O(n³) | Interval DP, Floyd-Warshall |
| 5,000 | O(n²) | 2-D DP |
| 10⁵ | O(n log n) | Sort, heap, binary search, segment tree |
| 10⁶ | O(n) | Two pointers, prefix sums, sieve |
| 10¹⁸ | O(log n) | Binary search on the answer, fast power |

---

## 3. Pattern triggers ⭐⭐⭐

```
"minimise the maximum" / "maximise the minimum"  → BINARY SEARCH ON THE ANSWER
sorted array, find a pair                        → two pointers
longest/shortest subarray with property          → sliding window
subarray sum = k, with negatives                 → prefix sum + hash map
next greater / largest rectangle                 → monotonic stack
sliding-window maximum                           → monotonic deque
k largest / k most frequent                      → heap of size k
median of a stream                               → two heaps
shortest path, unweighted / 0-1 / weighted       → BFS / 0-1 BFS / Dijkstra
negative weights or cycle detection              → Bellman-Ford
dependencies, ordering                           → topological sort
"are these connected", merging groups            → union-find
minimum cost to connect all                      → MST
count ways / optimal value, overlapping subcases → DP
n ≤ 20 and subsets                               → bitmask DP
prefix / dictionary / autocomplete               → trie
intervals: merge, min rooms                      → sort by start, sweep
cycle in a linked list, find duplicate           → Floyd tortoise-and-hare
LRU cache                                        → hash map + doubly linked list
```

---

## 4. The five code reflexes ⚠️

```
1. long long for products, prefix sums and binary-search midpoints
2. lo + (hi − lo)/2, never (lo + hi)/2
3. Dijkstra needs  if (du > d[u]) continue;
4. 0/1 knapsack 1-D: iterate weight DOWNWARD
5. ios::sync_with_stdio(false); cin.tie(nullptr);   /  input = sys.stdin.readline
```

**Edge cases, every single time:** `n = 0` · `n = 1` · all equal · all negative · empty string ·
single-node tree · duplicates · overflow · the maximum constraint.

---

## 5. OA strategy ⭐⭐

```
FIRST 5 MINUTES : read EVERY problem before writing any code. Rank by (confidence ÷ time).
                  Solve in YOUR order, not the given order.
PARTIAL SCORING : submit a correct brute force BEFORE optimising. ⭐ Correctness often scores
                  separately from performance (Codility definitely does).
TIME BOX        : no problem gets more than 25% of the total time on the first pass.
STUCK AT 2 MIN  : write the brute force. Something submitted beats nothing perfect.
MCQ SECTIONS    : if there is negative marking, skip anything below ~60% confidence.
                  If there is no negative marking, answer EVERYTHING. ⚠️ check which it is
LOCKED SECTIONS : if sections lock, do not plan to come back. Finish each as you go.
LAST 10 MINUTES : stop writing new code. Re-read your submissions for edge cases.
```

---

## 6. Interview protocol ⭐⭐

```
 1. REPEAT the problem back in your own words. Confirm the input/output format.
 2. ASK about constraints, duplicates, negatives, empty input, and what to return on invalid input.
 3. STATE a brute force and its complexity. Do not stay silent while thinking. ⚠️
 4. NAME the pattern. "This is binary search on the answer."
 5. Agree on the approach BEFORE coding. "Does that approach sound reasonable?"
 6. Code while narrating. Meaningful names. No clever one-liners.
 7. DRY-RUN on the given example, out loud, line by line.
 8. Volunteer edge cases yourself.
 9. State time and space complexity unprompted.
10. Offer one improvement you would make with more time.
```

> **When stuck:** say what you are considering and why it does not work. That invites a hint and
> shows reasoning. Silence gets you nothing. ⭐
> **When corrected:** "Let me re-check that" and genuinely re-verify. ⚠️ Do not fold instantly if
> you are right, and do not argue if you are wrong. Arcesium in particular probes this.

---

## 7. Your 60-second pitch ⭐⭐⭐

> ⚠️ **Write yours out and rehearse it aloud.** Do not improvise this tomorrow.

```
WHO I AM     : M.Tech CSE at IIT Madras, working at the intersection of compilers,
               GPU systems and deep learning.
WHAT I BUILT : _______________________________________________________________
               _______________________________________________________________
THE NUMBER   : _______________________________________________________________
WHY THIS ROLE: _______________________________________________________________
```

**Which projects to lead with — fill from the company folder before you close the laptop:**
```
Company tomorrow : ______________________
Project 1        : ______________________   (opening sentence: ____________________________)
Project 2        : ______________________
One number       : ______________________
Risk they will probe: ___________________   (my prepared answer: __________________________)
```

> ⭐ **The sentence that works for GPU/systems roles:** *"an irregular, data-dependent workload
> where the graph rewrites itself during traversal."*

---

## 8. Core-CS rapid fire ⭐⭐

```
Process vs thread     : separate address space vs shared; threads share code/data/heap, not
                        stack/registers/PC
Mutex vs semaphore    : ownership + mutual exclusion vs a counter/signal postable by anyone
Deadlock              : mutual exclusion · hold-and-wait · no preemption · circular wait →
                        break one; global lock ordering is the practical fix
Paging vs segmentation: fixed frames (internal fragmentation) vs variable (external)
Thrashing             : more paging than executing → reduce multiprogramming, working-set model
Belady's anomaly      : FIFO can fault MORE with more frames
False sharing         : two threads writing the same 64 B cache line → pad to 64 B

TCP vs UDP            : reliable/ordered/congestion-controlled/20 B vs none/8 B
3-way handshake       : SYN → SYN-ACK → ACK; both ISNs must be confirmed
Congestion control    : slow start (exponential) → AIMD → fast retransmit on 3 dup ACKs →
                        fast recovery; timeout resets cwnd to 1
HTTP/2 vs /3          : multiplexed streams over TCP vs QUIC/UDP removing head-of-line blocking

ACID                  : atomicity, consistency, isolation, durability (WAL for durability)
Isolation levels      : RU → RC (no dirty) → RR (no non-repeatable) → SER (no phantom)
Why B⁺-tree           : high fanout → shallow; linked leaves → range scans
Leftmost prefix       : index (a,b,c) serves a / a,b / a,b,c — not b alone
Normal forms          : 1NF atomic · 2NF no partial · 3NF no transitive · BCNF determinant = superkey
SQL execution order   : FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT
Window fn in WHERE    : not allowed — wrap in a subquery/CTE

4 pillars             : encapsulation (bundle + hide) · abstraction (expose what, hide how) ·
                        inheritance · polymorphism
SOLID                 : SRP · OCP · LSP · ISP · DIP
Overload vs override  : compile-time, different params vs run-time, same signature
```

---

## 9. ML rapid fire ⭐ (if the role is ML)

```
1% positives, predict-all-negative → 99% accuracy, recall 0. That is why accuracy is useless. ⭐
Imbalanced metrics   : PR-AUC, recall at fixed precision. ROC-AUC is optimistic.
PR-AUC baseline      : the positive class rate, not 0.5
MAE vs MSE           : median vs mean; MSE chases outliers
Bias-variance        : underfit (train high) vs overfit (gap). Bagging cuts variance,
                       boosting cuts bias
L1 vs L2             : sparse (corners on the axes) vs smooth shrinkage
Leakage              : fit the scaler on train only; SMOTE on the training fold only;
                       feature selection inside CV; no shuffling time series ⭐
Attention            : softmax(QKᵀ/√d_k)V — the √d_k keeps softmax out of saturation
Why positional enc.  : attention is permutation-invariant
Tabular data         → gradient boosting first, not a neural net
ALWAYS state the baseline next to your number
```

---

## 10. The last three lines ⭐

```
I do not need to be perfect. I need to be RELIABLE on what I already know.
A candidate who executes 70% of the syllabus flawlessly beats one who half-knows 100%.
Close the laptop. Sleep.
```

---

## Cross-references (for tonight only if something is genuinely blank)
```
Patterns in depth     : dsa-patterns-one-pager.md
Complexities          : complexity-table.md
Company specifics     : ../08_Company_Wise/<company>/
Your recurring errors : ../10_Mistake_Log_and_Revision/
Interview protocol    : ../07_Interviews/01_Technical_Round_Prep/
```
