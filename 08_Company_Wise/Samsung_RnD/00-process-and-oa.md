
# Samsung R&D Institute India (SRIB) — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **⭐⭐⭐ A format that suits you unusually well.** Samsung's defining round is **one hard
> algorithmic problem in about three hours, in C or C++**, sometimes with the standard library
> restricted. That rewards exactly what you have — strong C++, patience with a hard problem, and
> willingness to write a lot of code by hand — and penalises candidates who rely on memorised
> LeetCode patterns and library calls.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | Software Engineer; teams across AI, multimedia, networks, platform, security | ☐ |
| Eligibility | Varies; CGPA bar commonly around 7.0 | ☐ |
| Rounds | OA / coding test → technical interview(s) → HR | ☐ |
| Signature round | **One hard problem, ~3 hours**, C/C++ | ☐ |
| STL permitted? | ⚠️ **Restricted in some variants — verify explicitly** | ☐ |
| Other sections | Some tracks add aptitude + technical MCQ | ☐ |
| Locations | Bengaluru (SRIB), Noida (SRINoida), Delhi | ☐ |

⚠️ **The STL question is the single most important thing to verify.** If `std::vector`,
`std::queue` and `std::sort` are unavailable, you must be able to hand-roll a queue, a priority
queue and a sort. That is a different preparation and it cannot be improvised in the test.

---

## 2. The process (typical shape)

```
Resume shortlist → Coding test (the hard problem) → Technical interview 1 → (Technical 2) → HR
```

| Round | What it actually tests |
|---|---|
| Coding test | Can you solve one genuinely hard problem, correctly, in C/C++, with time to spare for edge cases |
| Technical 1 | DSA, core CS (OS/DBMS/networks), C/C++ depth, and your projects |
| Technical 2 | Deeper CS fundamentals or team-specific domain |
| HR | Fit, relocation, long-term plans |

---

## 3. What Samsung optimises for ⭐⭐⭐

> **Completing one hard thing properly**, rather than solving several easy things quickly. The
> three-hour format is a deliberate filter for persistence and careful implementation.

**The coding test's typical problem shapes:**
```
- BFS / DFS on a grid with state (multiple keys, portals, limited moves, fuel)
- Simulation with precise rules over many steps
- Backtracking with pruning (place N items subject to constraints)
- Bitmask DP (n ≤ 20) — "visit all targets in minimum cost"
- Shortest path with an extra dimension in the state (time, remaining budget, items held)
- Combinatorial search with memoisation
```

⭐ **The recurring shape: BFS/DFS or DP over an augmented state space.** The state is rarely just
`(row, col)` — it is `(row, col, keys_held)` or `(row, col, moves_left, direction)`. Recognising
that you need to *expand the state* is the whole insight in most Samsung problems.

**Topic emphasis, ranked:**
```
1. Graph traversal with augmented state (BFS/DFS) ⭐⭐⭐
2. Backtracking with pruning
3. Bitmask DP and other DP over small n
4. Careful simulation
5. Core CS fundamentals (interview round): OS, DBMS, networks
6. C/C++ depth
```

---

## 4. Your fit ⭐⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐⭐ — the format is in your favour** |
| Why | Strong C++, comfortable with long implementations, graph experience from Δ-stepping and CSR work |
| Biggest advantage | Three hours with no time pressure suits deep work over fast pattern-matching; and you have genuinely implemented graph algorithms, not just read them |
| Biggest risk | **Bitmask DP and backtracking** — your DP gap lands exactly here ⚠️; plus the STL restriction if it applies |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
1. Parallel SSSP on GPU        — a graph algorithm implemented from first principles; it directly
                                 demonstrates the skill the coding test selects for  ⭐
2. Points-to Analysis on GPU   — the flagship; a worklist fixpoint over a graph
3. Transformer from scratch    — if the team is AI/multimedia
```

⭐ **The line to use:** *"I implemented Δ-stepping shortest path in CUDA over CSR graphs, including
the worklist design — so the graph-traversal-with-state shape is something I've built rather than
just solved."*

**Narrative risk most likely here:** the CGPA, since Samsung often has an explicit bar.
→ `../../07_Interviews/04_HR_and_Behavioral/03-Difficult_Questions.md` §2

---

## 5. Tech stack and what to read

```
C, C++, Java, Android, Tizen, Linux, embedded platforms, on-device AI (Exynos NPU),
multimedia codecs, networking stacks

READ:
  □ Samsung Research blog / published papers if the team is AI-facing
  □ Which SRIB team you are applying to — the domains differ enormously  ⭐ ask the placement cell

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
Graph patterns      : ../../01_DSA/ (graphs, BFS/DFS with state)
DP ⚠️ your gap       : ../../01_DSA/ (DP, bitmask DP)
Core CS MCQ         : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/
C++ depth           : ../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md §6
OA archetype        : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Startups_Unicorns_and_Others.md §4
Projects            : ../../07_Interviews/02_Project_Deep_Dives/02-Parallel_SSSP_on_GPU.md
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
