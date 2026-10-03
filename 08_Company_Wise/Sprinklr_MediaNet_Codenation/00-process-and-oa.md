
# Sprinklr, Media.net and Codenation (Trilogy) — Process and OA Pattern

> Grouped because they share one defining property: **competitive-programming difficulty**. These
> are the hardest pure-algorithmic tests on the campus circuit, often harder than Google's, and
> they pay accordingly.

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **The honest posture: attempt, do not optimise for.** ⭐
> The preparation that makes you competitive here is the *same* preparation as for Google — DP,
> observation-driven problems, constraints-first thinking — so nothing is wasted. But if your DP is
> still weak, treat these as free lottery tickets rather than as targets.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | Product Engineer (Sprinklr), SDE (Media.net), Software Engineer (Codenation/Trilogy) | ☐ |
| Eligibility | Often no CGPA bar — the test *is* the filter ⭐ good for you | ☐ |
| Rounds | OA → 2-3 technical → HM/HR | ☐ |
| OA platform | HackerRank / HackerEarth / in-house | ☐ |
| Band | **Medium-Hard to Hard** ⚠️ competitive-programming style | ☐ |
| Duration | 90-180 min, 2-4 problems | ☐ |
| Languages | C++, Java, Python typically | ☐ |
| Locations | Gurugram (Sprinklr), Mumbai (Media.net), Bengaluru (Codenation) | ☐ |

⭐ **The eligibility point matters for you:** several of these companies have no CGPA cut-off
because they rely entirely on the test. If your 7.5 is filtering you out elsewhere, these are
companies where it does not.

---

## 2. The process (typical shape)

```
OA (hard algorithmic) → Technical ×2-3 (more hard problems) → HM/HR
```

| Round | What it actually tests |
|---|---|
| OA | Can you solve Medium-Hard problems under time pressure. Partial scoring is common |
| Technical 1-2 | More hard problems, live. Expect to be pushed past your comfort zone |
| Technical 3 / HM | Depth, sometimes design; occasionally a discussion of your OA solution |
| HR | Light |

⭐ **Sprinklr specifically** also runs machine-coding / product-engineering rounds for some tracks —
check which track you are applying to, because the preparation differs sharply.

---

## 3. What they optimise for ⭐⭐⭐

> **Raw algorithmic problem-solving.** No fundamentals MCQs, no aptitude, little project weight.
> The test is the filter and the bar is high. If you can solve the problems you get the offer;
> if you cannot, nothing else compensates.

**Topic emphasis, ranked:**
```
1. DYNAMIC PROGRAMMING ⚠️⭐⭐⭐ — the dominant topic and your gap
2. Graphs — shortest paths, MST, topological sort, union-find, strongly connected components
3. Greedy with a provable exchange argument
4. Binary search on the answer
5. Number theory and combinatorics with modular arithmetic — nCr mod p, sieve, modular inverse
6. Segment trees / BIT — more likely here than at Google ⭐
7. String algorithms — KMP, Z-algorithm, hashing, tries ⚠️ absent from your projects
8. Bitmask techniques
```

⭐ **The constraints-first habit is even more important here than at Google**, because the time
budget is tight and the intended complexity is usually unambiguous from the limits.

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐** |
| Why | Strong C++ and genuine graph-algorithm depth; no CGPA barrier |
| Biggest advantage | ⭐ Graph algorithms implemented from first principles (Δ-stepping, CSR, worklist design) is real depth that most candidates lack. Also: no CGPA filter |
| Biggest risk | ⚠️⚠️ **DP and string algorithms.** Both are absent from your resume and both are heavily weighted. This is the binding constraint |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
Project weight is LOW here. When asked:
1. Parallel SSSP on GPU        — Δ-stepping is a genuinely interesting algorithm to discuss
                                 with a competitive-programming audience  ⭐
2. Points-to Analysis on GPU   — the fixpoint/worklist framing
```

**Narrative risk most likely here:** none specifically. These companies care about the test.

---

## 5. Tech stack and what to read

```
Sprinklr   : Java, Node, React, MongoDB, Elasticsearch, Kafka — social/CX platform at scale
Media.net  : C++, Java, Python — ad-tech, real-time bidding at very high QPS ⭐ latency-sensitive
Codenation : product engineering, varied

READ:
  □ Codeforces Div 2 problems ⭐⭐⭐ — the single best proxy for this style
  □ AtCoder Beginner Contest D-F — excellent for DP
  □ Media.net: real-time bidding is genuinely latency-critical, which connects to your
    performance-engineering background ⭐ worth mentioning

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
OA archetype       : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Startups_Unicorns_and_Others.md §2
                     (Archetype D — hard algorithmic)
Google prep (same) : ../Google/01-prep-and-questions.md  ⭐ the plans overlap almost entirely
DSA, DP ⚠️          : ../../01_DSA/
Combinatorics      : ../../06_Online_Assessments/02_Aptitude_and_Quant/Probability_and_Combinatorics.md
Interview protocol : ../../07_Interviews/01_Technical_Round_Prep/00-Interview_Protocol.md
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
