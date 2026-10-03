
# Google — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **⭐⭐ High effort, low odds — but the effort transfers.** Google has the hardest algorithmic bar
> on your list and the binding constraint is exactly your gap (DP and observation-driven problems).
>
> ⭐ **The strategic argument for preparing anyway:** unlike quant preparation, Google preparation
> is not specialised. It is the general DSA work you need for Amazon, Microsoft, Flipkart, Arcesium
> and Sprinklr, done harder. Nothing is wasted if you fail.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | SWE, SWE-Early Career, STEP (intern) | ☐ |
| Eligibility | No published CGPA bar; resume screen is the filter | ☐ |
| Rounds | OA → 1-2 phone/online technical → loop (4-5 rounds) → hiring committee → team match | ☐ |
| OA platform | Google's own assessment portal | ☐ |
| OA duration | 60-90 min, **2 coding problems** | ☐ |
| Band | **Medium-to-Hard** ⚠️ harder than Amazon/Microsoft | ☐ |
| Languages | C++, Java, Python, Go, JS, Kotlin | ☐ |
| Locations | Bengaluru, Hyderabad, Pune, Gurugram | ☐ |

⚠️ **Hiring committee and team match** mean an offer is not decided by your interviewers alone, and
the timeline is longer than other companies. Budget for that.

---

## 2. The process (typical shape)

```
Resume screen → OA (2 problems) → Phone screen(s) → Loop ×4-5 → Hiring committee → Team match
```

| Round | What it actually tests |
|---|---|
| OA | Two Medium-Hard problems, observation-driven |
| Phone screen | Live coding in a plain shared document — **no autocomplete, no compiler** ⚠️ |
| Loop coding ×3 | The same, harder, with heavy emphasis on thinking aloud |
| Googleyness | Collaboration, ambiguity, dealing with conflict — STAR answers |
| Hiring committee | Reviews written feedback; you are not present |

---

## 3. What Google optimises for ⭐⭐⭐

> **One non-obvious observation per problem.** Google problems are usually *not* a named algorithm
> in disguise. The intended solution rests on noticing a property — monotonicity, an invariant,
> parity, optimal substructure — and finding that property *is* the problem.

**The single most useful preparation habit ⭐⭐⭐: read the constraints first.**

| Constraint | Intended complexity | Technique |
|---|---|---|
| `n ≤ 10` | `O(n!)` | Permutations, full search |
| `n ≤ 20-25` | `O(2ⁿ)` | **Bitmask DP**, meet in the middle |
| `n ≤ 100-500` | `O(n³)` | Floyd-Warshall, interval DP |
| `n ≤ 5,000` | `O(n²)` | 2-D DP |
| `n ≤ 10⁵-10⁶` | `O(n log n)` | Sort, heap, binary search, segment tree |
| `n ≤ 10⁷-10⁸` | `O(n)` | Two pointers, prefix sums, sieve |
| `n ≤ 10¹⁸` | `O(log n)` | Binary search on the answer, maths, matrix exponentiation |

**Topic emphasis, ranked:**
```
1. DYNAMIC PROGRAMMING ⭐⭐⭐ — 1-D, 2-D, on trees, bitmask, digit DP. The most Google-ish topic
                               and your biggest gap ⚠️
2. Graphs — BFS/DFS, Dijkstra, 0-1 BFS, topological sort, union-find, MST
3. Binary search on the answer ⭐ — "minimise the maximum" / "maximise the minimum"
4. Greedy WITH an exchange argument (you must be able to justify it)
5. Combinatorics and number theory with modular arithmetic
6. Trees — LCA, diameter, rerooting
7. Prefix sums, two pointers, monotonic structures
8. Segment tree / BIT (rarer in OA, present in Kick Start-style rounds)
```

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐** |
| Why | Strong C++ and genuine graph-algorithm experience; but the DP and observation-driven bar is the highest on your list |
| Biggest advantage | ⭐ Graph algorithms — you implemented Δ-stepping and CSR from first principles, which is deeper than most candidates' graph knowledge |
| Biggest risk | ⚠️⚠️ **DP, and live coding in a plain document.** Both are unpractised. Also: Google rounds are the most communication-heavy, and silence is scored badly |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
Google weights projects LESS than other companies — the loop is mostly coding. But when asked:
1. Points-to Analysis on GPU   — the algorithmic framing: a monotone fixpoint over a graph
2. Parallel SSSP on GPU        — Δ-stepping is a genuinely interesting algorithm to discuss
```

**Narrative risk most likely here:** none specifically — Google asks fewer resume questions. The
real risk is the coding bar.

---

## 5. Tech stack and what to read

```
C++, Java, Python, Go; internal infrastructure (Borg, Spanner, Bigtable, MapReduce lineage)

READ:
  □ The Kick Start archive ⭐⭐⭐ — the single best proxy for Google's problem style, even though
    the competition itself is retired. Do timed rounds.
  □ Google Research / Google Cloud blog if the role names a domain
  □ Codeforces Div 2 A-C ⭐ — trains the "find the observation" muscle better than LeetCode

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
OA format in depth ⭐ : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Google.md
                        (the constraints table, the 7-step attack method)
DSA, DP ⚠️            : ../../01_DSA/
Combinatorics         : ../../06_Online_Assessments/02_Aptitude_and_Quant/Probability_and_Combinatorics.md
Live-coding practice  : ../../07_Interviews/01_Technical_Round_Prep/00-Interview_Protocol.md §12
Googleyness / STAR    : ../../07_Interviews/04_HR_and_Behavioral/02-STAR_Story_Bank.md
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
