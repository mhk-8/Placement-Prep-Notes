
# Arcesium — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **⭐⭐ Strong IIT recruiter, good fit, high bar.** Arcesium (a D. E. Shaw spin-off building
> post-trade financial technology) sits between a product company and a quant firm: real DSA at a
> high bar, serious CS fundamentals, and a dash of probability. Good package, and they hire
> consistently from IIT Madras.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | Software Engineer, Analyst (Engineering), Site Reliability | ☐ |
| Eligibility | Often a CGPA bar around 7.0-7.5 ⚠️ check — you are near some bars | ☐ |
| Rounds | OA → 2-3 technical rounds → HR/fit | ☐ |
| OA platform | HackerRank / in-house | ☐ |
| OA content | Coding (2-3, Medium to Medium-Hard) + MCQs on CS fundamentals, sometimes aptitude/probability | ☐ |
| Languages | C++, Java, Python typically permitted | ☐ |
| Locations | Hyderabad, Bengaluru | ☐ |

---

## 2. The process (typical shape)

```
Resume shortlist → OA → Technical 1 (DSA) → Technical 2 (DSA + fundamentals + projects) → HR/fit
```

| Round | What it actually tests |
|---|---|
| OA | Medium to Medium-Hard DSA, plus CS fundamentals MCQs |
| Technical 1 | DSA depth; clean code; complexity reasoning |
| Technical 2 | DSA + OS/DBMS/networks + your projects; sometimes a light design question |
| HR / fit | Why fintech, long-term plans, communication |

---

## 3. What Arcesium optimises for ⭐⭐⭐

> **Correctness and rigour.** They build systems where a wrong number has financial consequences,
> so they screen hard for people who handle edge cases, reason about complexity precisely, and do
> not hand-wave. Expect to be pushed on "are you sure?" more than at a typical product company.

**Topic emphasis, ranked:**
```
1. DSA — Medium to Medium-Hard; arrays, strings, hashing, trees, graphs, DP
2. CS fundamentals — OS, DBMS (they are a data company: SQL matters ⭐), networks
3. Complexity analysis, precisely stated
4. Problem-solving under questioning — they will challenge a correct answer to see if you fold
5. Some probability / quantitative reasoning in the OA
6. Projects, with an emphasis on correctness and validation
```

⭐ **The Arcesium-specific behaviour to prepare for:** being asked "are you sure that's right?"
when you *are* right. The correct response is to re-verify calmly and either confirm with reasoning
or find your error — not to immediately change your answer. Folding under pressure on a correct
answer is a red flag for them.

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐** |
| Why | Strong C++, systems rigour, and a project with genuine validation discipline (Soot/DaCapo differential testing, statistical significance testing in the IR project) |
| Biggest advantage | ⭐ **Your validation instincts.** The IR project's Wilcoxon test and the points-to project's exact-match validation against a reference implementation are exactly the rigour they screen for — most candidates have nothing like this |
| Biggest risk | The DSA bar is higher than Tier B; your DP gap matters here ⚠️. Also check the CGPA cut-off |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
1. Information Retrieval Engine  — ⭐⭐ lead with this, counter-intuitively. Six systems
                                   benchmarked, Wilcoxon signed-rank with p and Cohen's d.
                                   "A better number is not a real improvement" is precisely
                                   the Arcesium mindset.
2. Points-to Analysis on GPU     — the validation story: exact match against Soot on DaCapo,
                                   because a soundness-critical analysis cannot be approximately right
3. Parallel SSSP on GPU          — if they want algorithms depth
```

⭐ **The sentence to use:** *"In the IR project I improved MAP by 22.8%, but what I'm actually
pleased with is that I ran a Wilcoxon signed-rank test across 225 queries and reported Cohen's d —
because with that many samples, a better number isn't automatically a real improvement."*

**Narrative risk most likely here:** the CGPA, if they have a bar.
→ `../../07_Interviews/04_HR_and_Behavioral/03-Difficult_Questions.md` §2

---

## 5. Tech stack and what to read

```
Java (significant), Python, C++, SQL and large-scale data processing, AWS, Kubernetes,
distributed data pipelines, post-trade financial systems

⚠️ JAVA IS SIGNIFICANT HERE. See ../../07_Interviews/03_Resume_and_Portfolio/02-Skill_Claims_Defence.md §3
   for the honest framing of your Java claim. If Arcesium is high on your list, consider the
   two-week Java sprint.

READ:
  □ What post-trade actually means — a one-paragraph understanding is enough and it shows interest
  □ Arcesium's engineering blog / tech talks, if available
  □ SQL window functions ⭐ — a data company will ask

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
DSA                : ../../01_DSA/
DBMS and SQL ⭐     : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/DBMS.md
                     ../../09_Cheatsheets/ (if SQL cheatsheet exists)
Core CS MCQ        : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/
Probability        : ../../05_AI_ML/02_Probability_and_Statistics/
Java framing ⚠️     : ../../07_Interviews/03_Resume_and_Portfolio/02-Skill_Claims_Defence.md §3
Projects           : ../../07_Interviews/02_Project_Deep_Dives/06-Information_Retrieval_Engine.md
OA archetype       : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Startups_Unicorns_and_Others.md
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
