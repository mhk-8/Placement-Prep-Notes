# Syllabus Checklist

> Every topic that can appear in an OA or interview, with a confidence rating.
> **Scoring:** 0 = never seen · 1 = heard of it · 2 = can follow an explanation · 3 = can solve with hints · 4 = can solve cold · 5 = can teach it and handle follow-ups.
> **Interview-ready = 4.** OA-ready = 4 *under time*. Anything at 3 or below on a P1 row is your next study session.

**How to use:** rate everything honestly in Week 0. Re-rate at each phase gate (end of W4, W8, W10). Sort your study order by `Priority` then by lowest score. Do not re-rate more often than that — daily re-rating becomes procrastination.

Legend: **P1** never cut · **P2** cut only in the last two weeks · **P3** cut freely · *(ML)* = P1 if targeting AI/ML roles

---

## A. DSA — `01_DSA`

| # | Topic | Pri | W0 | W4 | W8 | W10 | Notes |
|---|---|---|---|---|---|---|---|
| A1 | Big-O, recurrences, master theorem, amortised analysis | P1 | | | | | |
| A2 | Modular arithmetic, GCD, sieve, nCr mod p | P2 | | | | | |
| A3 | Arrays: prefix sums, difference arrays, 2-D prefix | P1 | | | | | |
| A4 | Kadane and max-subarray variants | P1 | | | | | |
| A5 | Strings: building costs, palindromes, anagrams | P1 | | | | | |
| A6 | KMP / Z-function / rolling hash | P3 | | | | | |
| A7 | Hash maps: frequency, two-sum family, prefix-sum + map | P1 | | | | | |
| A8 | Two pointers on sorted data; fast/slow pointers | P1 | | | | | |
| A9 | Sliding window: fixed and variable; at-most-K trick | P1 | | | | | |
| A10 | Binary search: exact, lower/upper bound, rotated | P1 | | | | | |
| A11 | Binary search on the answer (min-max / max-min) | P1 | | | | | |
| A12 | Sorting algorithms: complexity, stability, which is used where | P1 | | | | | MCQ-heavy |
| A13 | Custom comparators; sort-then-greedy | P1 | | | | | |
| A14 | Counting/radix/bucket sort; quickselect; inversion count | P2 | | | | | |
| A15 | Recursion: state, base case, recursion tree | P1 | | | | | |
| A16 | Backtracking: subsets, permutations, N-Queens, word search | P1 | | | | | |
| A17 | Linked list: reverse, cycle, merge, dummy head | P1 | | | | | |
| A18 | LRU cache (DLL + hashmap) | P1 | | | | | Asked verbatim |
| A19 | Stacks: parentheses, expression evaluation, min-stack | P1 | | | | | |
| A20 | Monotonic stack: next greater, histogram, rain water | P1 | | | | | High yield |
| A21 | Monotonic deque: sliding-window maximum | P1 | | | | | |
| A22 | Tree traversals: all four, recursive **and** iterative | P1 | | | | | |
| A23 | BFS/level order, views, zigzag | P1 | | | | | |
| A24 | Height, diameter, balanced check, path sums | P1 | | | | | |
| A25 | LCA (binary tree and BST) | P1 | | | | | |
| A26 | BST: validate, insert, delete, k-th smallest | P1 | | | | | |
| A27 | Serialize/deserialize; build from traversals | P2 | | | | | |
| A28 | Morris traversal (O(1) space) | P3 | | | | | Follow-up only |
| A29 | Heaps: top-K, k-th largest, merge k lists | P1 | | | | | |
| A30 | Two-heap running median | P2 | | | | | |
| A31 | Tries: insert/search/prefix; word dictionary | P2 | | | | | |
| A32 | Binary trie for maximum XOR | P3 | | | | | |
| A33 | Graph representations; BFS/DFS; components; bipartite | P1 | | | | | |
| A34 | Cycle detection: directed and undirected | P1 | | | | | |
| A35 | Topological sort (Kahn + DFS); course-schedule family | P1 | | | | | Very high yield |
| A36 | Grid BFS, multi-source BFS, 0-1 BFS | P1 | | | | | |
| A37 | Dijkstra | P1 | | | | | |
| A38 | Bellman-Ford; Floyd-Warshall; negative cycles | P2 | | | | | |
| A39 | MST: Kruskal, Prim | P2 | | | | | |
| A40 | Union-Find with path compression + union by rank | P1 | | | | | |
| A41 | Bridges, articulation points, SCC | P3 | | | | | |
| A42 | DP: state / transition / base case discipline | P1 | | | | | The differentiator |
| A43 | 1-D DP: stairs, house robber, LIS (n log n) | P1 | | | | | |
| A44 | Knapsack: 0/1, unbounded, subset-sum, partition | P1 | | | | | |
| A45 | 2-D grid DP: paths, min path sum, obstacles | P1 | | | | | |
| A46 | String DP: LCS, edit distance, palindromic substrings | P1 | | | | | |
| A47 | Interval DP: matrix chain, burst balloons | P2 | | | | | |
| A48 | Bitmask DP: TSP, assignment | P2 | | | | | |
| A49 | Digit DP | P3 | | | | | |
| A50 | Memo vs tabulation vs space optimisation | P1 | | | | | |
| A51 | Greedy: exchange argument, interval scheduling, Huffman | P1 | | | | | |
| A52 | Bit manipulation: masks, n&(n-1), XOR tricks, popcount | P2 | | | | | MCQ-heavy |
| A53 | Intervals: merge, insert, non-overlapping, meeting rooms | P1 | | | | | |
| A54 | Sweep line with +1/-1 events | P2 | | | | | |
| A55 | Fenwick / BIT | P3 | | | | | |
| A56 | Segment tree (+ lazy propagation) | P3 | | | | | |
| A57 | Sparse table / static RMQ | P3 | | | | | |

---

## B. Core CS — `02_Core_CS`

### B1. Operating Systems
| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| B1.1 | Process vs thread; PCB; context switch cost | P1 | | | | |
| B1.2 | Process states diagram | P1 | | | | |
| B1.3 | Scheduling: FCFS, SJF, SRTF, RR, priority, MLFQ | P1 | | | | |
| B1.4 | **Scheduling numericals** (AT / WT / TAT / response) | P1 | | | | |
| B1.5 | Race conditions; critical section; Peterson's | P1 | | | | |
| B1.6 | Mutex vs semaphore vs monitor | P1 | | | | |
| B1.7 | Producer-consumer, reader-writer, dining philosophers | P1 | | | | |
| B1.8 | Deadlock: 4 conditions, prevention, avoidance, Banker's | P1 | | | | |
| B1.9 | Paging, segmentation, virtual memory | P1 | | | | |
| B1.10 | TLB; **effective access time numericals** | P1 | | | | |
| B1.11 | Page replacement: FIFO, LRU, Optimal; Belady's anomaly | P1 | | | | |
| B1.12 | Thrashing; working set | P2 | | | | |
| B1.13 | File systems, inodes, journaling | P2 | | | | |
| B1.14 | IPC: pipes, shared memory, message queues | P2 | | | | |
| B1.15 | fork/exec; zombie and orphan processes | P2 | | | | |
| B1.16 | "What happens when you run a program" — full narrative | P1 | | | | |

### B2. DBMS & SQL
| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| B2.1 | ER model → relational schema | P1 | | | | |
| B2.2 | Keys: super, candidate, primary, foreign, composite | P1 | | | | |
| B2.3 | Functional dependencies, closure, lossless decomposition | P1 | | | | |
| B2.4 | Normal forms 1NF→BCNF; when to denormalise | P1 | | | | |
| B2.5 | Relational algebra | P2 | | | | |
| B2.6 | SQL: all join types, self-join, anti-join | P1 | | | | |
| B2.7 | GROUP BY / HAVING / aggregations | P1 | | | | |
| B2.8 | Window functions; RANK vs DENSE_RANK vs ROW_NUMBER | P1 | | | | |
| B2.9 | CTEs and recursive CTEs | P2 | | | | |
| B2.10 | Correlated subqueries | P1 | | | | |
| B2.11 | NULL semantics (three-valued logic) | P1 | | | | |
| B2.12 | Indexes: B+ tree, hash, clustered vs non-clustered | P1 | | | | |
| B2.13 | When an index *hurts*; EXPLAIN basics | P2 | | | | |
| B2.14 | ACID; isolation levels; dirty/non-repeatable/phantom reads | P1 | | | | |
| B2.15 | Concurrency control: 2PL, timestamp ordering, MVCC | P2 | | | | |
| B2.16 | SQL vs NoSQL; CAP theorem | P2 | | | | |
| B2.17 | Canonical queries: N-th highest salary, find duplicates, manager hierarchy | P1 | | | | |

### B3. Computer Networks
| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| B3.1 | OSI vs TCP/IP layers; what each layer does | P2 | | | | |
| B3.2 | TCP vs UDP; when each is chosen | P2 | | | | |
| B3.3 | Three-way handshake; four-way teardown; TIME_WAIT | P2 | | | | |
| B3.4 | Flow control; congestion control (slow start, AIMD) | P2 | | | | |
| B3.5 | IP addressing, **subnetting arithmetic**, CIDR, NAT | P2 | | | | |
| B3.6 | DNS resolution end to end | P2 | | | | |
| B3.7 | HTTP/1.1 vs 2 vs 3; methods, status codes, cookies | P2 | | | | |
| B3.8 | HTTPS / TLS handshake; symmetric vs asymmetric | P2 | | | | |
| B3.9 | ARP, DHCP, ICMP; well-known ports | P3 | | | | |
| B3.10 | Routing: distance vector vs link state | P3 | | | | |
| B3.11 | "What happens when you type google.com" | P1 | | | | |

### B4. OOP
| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| B4.1 | Four pillars, each with a non-textbook example | P1 | | | | |
| B4.2 | Overloading vs overriding; static vs dynamic dispatch | P1 | | | | |
| B4.3 | Abstract class vs interface | P1 | | | | |
| B4.4 | Composition over inheritance; diamond problem | P1 | | | | |
| B4.5 | SOLID — a violation and a fix for each | P1 | | | | |
| B4.6 | Virtual functions / vtables | P2 | | | | |
| B4.7 | Constructors, copy/move (C++), GC (Java/Python) | P2 | | | | |
| B4.8 | Output-prediction snippets with inheritance | P1 | | | | MCQ-heavy |

### B5. Architecture · B6. Compilers/TOC · B7. Software Engineering
| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| B5.1 | IEEE 754; number systems | P3 | | | | |
| B5.2 | Pipelining: hazards, forwarding, **speedup numericals** | P3 | | | | |
| B5.3 | Cache: mapping, hit/miss, write-through vs write-back | P2 | | | | |
| B5.4 | Memory hierarchy, locality, Amdahl's law | P3 | | | | |
| B6.1 | Compilation phases | P3 | | | | |
| B6.2 | Regex ↔ DFA/NFA; state counting | P3 | | | | |
| B6.3 | CFGs, parsing, ambiguity | P3 | | | | |
| B6.4 | Decidability; P/NP/NP-complete | P3 | | | | |
| B7.1 | Git: merge vs rebase, reset vs revert, conflicts | P1 | | | | |
| B7.2 | Testing: unit/integration/e2e, mocking | P2 | | | | |
| B7.3 | Docker + CI/CD basics | P2 | | | | |

---

## C. Languages — `03_Languages`

| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| C1 | Primary language: all standard containers + complexities | P1 | | | | |
| C2 | Primary language: sorting with custom comparators | P1 | | | | |
| C3 | Primary language: fast I/O idiom, memorised | P1 | | | | |
| C4 | Overflow / recursion-limit / precision traps | P1 | | | | |
| C5 | SQL fluency (separate OA section at many firms) | P1 | | | | |
| C6 | Language internals for interviews (GIL / JVM / RAII) | P2 | | | | |
| C7 | numpy + pandas fluency *(ML)* | P2 | | | | |

---

## D. System Design — `04_System_Design`

| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| D1 | LLD framework: requirements → entities → classes → code | P2 | | | | |
| D2 | LLD problems: parking lot, BookMyShow, Splitwise, elevator | P2 | | | | |
| D3 | Design patterns: top 6 (Singleton, Factory, Builder, Strategy, Observer, Decorator) | P2 | | | | |
| D4 | HLD framework + estimation numbers | P3 | | | | |
| D5 | Building blocks: LB, cache, sharding, replication, queues | P2 | | | | |
| D6 | CAP; consistency models; consistent hashing; rate limiting | P2 | | | | |
| D7 | 3 HLD case studies written up end to end | P3 | | | | |

---

## E. AI / ML — `05_AI_ML` *(whole section is P1 for AI/ML roles, P3 otherwise)*

| # | Topic | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| E1 | Linear algebra: rank, eigen, SVD, projections | *(ML)* | | | | |
| E2 | Matrix calculus; gradients by hand | *(ML)* | | | | |
| E3 | Optimisation: GD, SGD, momentum, Adam | *(ML)* | | | | |
| E4 | Probability: Bayes, distributions, expectation/variance | *(ML)* | | | | |
| E5 | CLT; MLE vs MAP | *(ML)* | | | | |
| E6 | Hypothesis testing; p-values; A/B test design | *(ML)* | | | | |
| E7 | Linear/logistic regression; L1 vs L2 | *(ML)* | | | | |
| E8 | Bias-variance; overfitting diagnosis; learning curves | *(ML)* | | | | |
| E9 | Trees, random forest, boosting (GBM/XGBoost) | *(ML)* | | | | |
| E10 | SVM: margin, kernels, C and gamma | *(ML)* | | | | |
| E11 | kNN, Naive Bayes and their assumptions | *(ML)* | | | | |
| E12 | Clustering: k-means, DBSCAN, GMM/EM | *(ML)* | | | | |
| E13 | PCA (derived, not recited); t-SNE | *(ML)* | | | | |
| E14 | **Metrics**: precision/recall/F1/ROC-AUC/PR-AUC — which, when | *(ML)* | | | | |
| E15 | Cross-validation; data leakage; class imbalance | *(ML)* | | | | |
| E16 | MLP + **backprop derived by hand** | *(ML)* | | | | |
| E17 | Activations; vanishing/exploding gradients | *(ML)* | | | | |
| E18 | Dropout, weight decay, batch/layer norm | *(ML)* | | | | |
| E19 | CNNs: conv arithmetic, receptive field, param counting | *(ML)* | | | | |
| E20 | RNN/LSTM/GRU | P3 | | | | |
| E21 | Attention & transformers: Q/K/V, multi-head, positional | *(ML)* | | | | |
| E22 | Tokenisation; BERT vs GPT; pretraining objectives | *(ML)* | | | | |
| E23 | Fine-tuning, LoRA/PEFT, RLHF/DPO | *(ML)* | | | | |
| E24 | Decoding params; RAG pipeline; vector DBs | *(ML)* | | | | |
| E25 | LLM evaluation; hallucination; inference cost | *(ML)* | | | | |
| E26 | CV: ResNet, detection (YOLO/IoU/NMS), segmentation | P3 | | | | |
| E27 | MLOps: serving, drift, monitoring, retraining | P2 | | | | |
| E28 | ML system design framework + 3 case studies | *(ML)* | | | | |
| E29 | pandas/numpy wrangling; feature engineering; encoding | *(ML)* | | | | |

---

## F. OA & Interview readiness — `06` / `07`

| # | Item | Pri | W0 | W4 | W8 | W10 |
|---|---|---|---|---|---|---|
| F1 | Aptitude: percentages, ratios, TSD, time-work, SI/CI | P1 | | | | |
| F2 | Aptitude: P&C, probability, number system, DI | P1 | | | | |
| F3 | Reasoning: series, arrangements, blood relations, clocks | P2 | | | | |
| F4 | Classic puzzles (10 of them, cold) | P2 | | | | |
| F5 | Verbal ability | P3 | | | | |
| F6 | Core-CS MCQ banks, timed, with negative marking | P1 | | | | |
| F7 | Time-allocation strategy rehearsed in 3+ mocks | P1 | | | | |
| F8 | 90-second self-introduction, rehearsed | P1 | | | | |
| F9 | Every resume line defensible to 3 "why"s | P1 | | | | |
| F10 | Project deep-dive files written (one per project) | P1 | | | | |
| F11 | 6 STAR stories written and rehearsed | P1 | | | | |
| F12 | Salary / relocation / bond / competing-offer answers | P2 | | | | |
| F13 | 5 questions to ask the interviewer | P2 | | | | |
| F14 | 2+ peer mock interviews completed | P1 | | | | |

---

## Scoring summary

> Recompute at each gate. Count only P1 rows; P2/P3 are informational.

| Gate | Date | P1 rows at ≥4 | P1 rows at ≤3 | Verdict |
|---|---|---|---|---|
| W0 baseline | 20 Sep | | | |
| Gate A | 18 Oct | | | |
| Gate B | 15 Nov | | | |
| Gate C | 29 Nov | | | |

**Target: every P1 row at 4+ by Gate C.** If ten or more P1 rows sit at ≤3 on 29 Nov, stop adding topics and spend the remaining days on those ten only.
