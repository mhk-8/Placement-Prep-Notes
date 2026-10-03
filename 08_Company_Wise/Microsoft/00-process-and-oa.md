
# Microsoft — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **⭐⭐ Good fit, well-understood process.** C++ is welcome, the DSA band is squarely Medium, and
> the Codility scoring model rewards exactly the edge-case discipline your validation habits
> already give you. Several Indian campuses also run a **Group Fly** paper round — check for it.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | SDE, SDE-Intern conversion, Research SDE | ☐ |
| Eligibility | Usually no hard CGPA bar on campus | ☐ |
| Rounds | OA → (Group Fly) → 2-3 technical → AA round | ☐ |
| OA platform | **Codility** most often; sometimes HackerRank | ☐ |
| OA duration | 60-90 min, 2-3 coding problems, no MCQs in the standard campus OA | ☐ |
| Scoring | **Correctness + Performance scored separately** ⭐ | ☐ |
| Group Fly? | ⚠️ **hand-written code on paper** on some campuses — verify | ☐ |
| Locations | Hyderabad, Bengaluru, Noida | ☐ |

---

## 2. The process (typical shape)

```
OA (Codility, 2-3 problems) → (Group Fly paper round) → Technical ×2-3 → AA round
```

| Round | What it actually tests |
|---|---|
| OA | Correct code **plus** edge cases — Codility scores both separately ⭐ |
| Group Fly | Hand-written complete programs in 30-45 min. A genuinely different skill ⚠️ |
| Technical 1-2 | DSA (arrays, strings, linked lists weighted), CS fundamentals, projects |
| AA ("As Appropriate") | Senior/hiring-manager round: design, depth, behaviour. Can be the hardest |

---

## 3. What Microsoft optimises for ⭐⭐⭐

> **Clean, complete, correct implementation.** Microsoft asks fewer exotic algorithms than Google
> and more "implement this specification exactly" problems than Amazon. Edge cases are where the
> marks are.

**The Codility scoring model — the thing candidates misunderstand ⚠️⭐⭐**
```
Task score = (Correctness + Performance) / 2

  → A correct O(n²) where O(n log n) was intended scores ~50-60%, NOT zero.
    ALWAYS SUBMIT SOMETHING CORRECT.
  → Edge cases are weighted heavily: empty, single element, all-equal, max constraints,
    negatives, integer overflow.
  → Use the custom-test box. Run: empty, size 1, all identical, maximum size, negatives.
```

**Topic emphasis, ranked:**
```
1. String manipulation — parse, transform, compress, validate, format
2. Arrays with a constraint twist — rotate, rearrange, missing/duplicate
3. Hash map + frequency
4. Linked lists ⭐ Microsoft asks these more than most — reverse in k-groups, cycle, LRU cache
5. Trees and BST
6. Precise simulation — "apply this spec exactly for N steps"  ⭐ the Microsoft signature
7. Stacks, matrix traversal, bit manipulation, DP classics
```

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐** |
| Why | C++ welcome; Medium band is reachable; your validation discipline suits the Codility model |
| Biggest advantage | ⭐ Edge-case habits. Your projects involved exact-match validation and tolerance testing — write the four edge-case tests before optimising and you will outscore people who are better at the algorithm |
| Biggest risk | **Group Fly** if it runs (hand-writing code is unpractised), and linked-list implementation fluency |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
1. Points-to Analysis on GPU   — frame around correctness and validation, not compiler theory
2. Parallel SSSP on GPU        — the engineering decision (Near/Far worklist) is the story
3. Image Preprocessing on GPU  — if they want precise-specification implementation  ⭐ it is
                                 literally a "match the reference exactly" project
```

**Narrative risk most likely here:** no industry internship.

---

## 5. Tech stack and what to read

```
C#, .NET, C++, TypeScript, Azure, SQL Server, Windows internals (team-dependent)

READ:
  □ Codility's free demo task ⭐ — do it once so the interface is not new
  □ The Azure or team-specific product page if the role names one

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
OA format in depth ⭐ : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Microsoft.md
                        (the Codility scoring model, Group Fly marking scheme)
DSA                  : ../../01_DSA/
CS fundamentals      : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/
Design (AA round)    : ../../04_System_Design/
Projects             : ../../07_Interviews/02_Project_Deep_Dives/
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
