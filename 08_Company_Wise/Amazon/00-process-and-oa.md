
# Amazon — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **⭐⭐ The highest-volume recruiter on your list.** Amazon makes more campus offers than anyone
> else, which makes it the single best expected-value target even though your GPU work is neutral
> here. The process is heavily standardised and therefore highly preparable.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | SDE-1, Applied Scientist (separate funnel ⭐) | ☐ |
| Eligibility | Usually no hard CGPA bar on campus | ☐ |
| Rounds | OA → 1-2 online technical → loop (3-5 rounds incl. Bar Raiser) | ☐ |
| OA platform | Amazon's own portal (HackerRank-style engine) | ☐ |
| OA sections | Debugging · **Coding (2 problems)** · Work Style · Work Simulation · (sometimes reasoning) | ☐ |
| Partial scoring | **Yes** ⭐ — changes your whole strategy | ☐ |
| Languages | C, C++, Java, Python, C#, JS and more | ☐ |
| Locations | Bengaluru, Hyderabad, Chennai, Pune, Delhi NCR | ☐ |

---

## 2. The process (typical shape)

```
OA (debugging + 2 coding + Work Style + Work Simulation)
       ↓
Online technical round(s) — DSA + behavioural woven together  ⚠️ the LPs start here
       ↓
Loop: 3-5 rounds — 2 DSA, 1 LLD/HLD, 1 Bar Raiser
       ↓
Offer
```

⚠️ **Every round at Amazon is partly behavioural.** A technical interviewer will spend ten minutes
on a Leadership Principle story before the coding problem. Candidates who treat the technical rounds
as purely technical lose marks they do not know they lost.

⭐ **The Bar Raiser** is an interviewer from outside the hiring team with veto power, specifically
checking that you raise the bar rather than merely clear it. They weight the LP stories heavily.

---

## 3. What Amazon optimises for ⭐⭐⭐

> **Pattern recognition under time pressure, plus demonstrable alignment with the Leadership
> Principles.** Amazon does not ask exotic algorithms. It asks well-known Medium patterns wrapped
> in a business story, and it asks you to prove — with specific stories — that you behave the way
> they want.

**Topic emphasis, ranked:**
```
1. Hash map + counting
2. Two pointers / sliding window
3. Heaps / top-K  ⭐ "K closest points to origin" is an Amazon classic
4. Graph BFS/DFS on a grid (islands, rotting oranges, shortest path)
5. Binary search on the answer  ⭐⭐ "minimum capacity to ship packages in D days" — know it cold
6. Sorting + greedy with intervals
7. Trees, DP (1-D and simple 2-D), tries, union-find, monotonic stack
```

**The Leadership Principles that come up most:**
```
Customer Obsession · Ownership · Bias for Action · Dive Deep · Deliver Results ·
Insist on the Highest Standards · Have Backbone (Disagree and Commit) · Invent and Simplify
```

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐** |
| Why | Standard DSA bar you can clear; your systems depth is neutral rather than valued |
| Biggest advantage | **Dive Deep** is your natural LP — a year on one hard problem, profiler-driven, validated exactly. That story writes itself |
| Biggest risk | ⚠️ **Behavioural preparation.** Amazon weights LP stories as heavily as code, and your STAR bank has gaps (leadership/conflict). Also DP |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
1. Points-to Analysis on GPU   — frame it for DIVE DEEP and OWNERSHIP, not for the compiler content.
                                 "I owned one hard problem for a year and I know it at the level of
                                  profiler counters."  ⭐
2. Parallel SSSP on GPU        — frame for DELIVER RESULTS (concrete scale numbers)
3. Transformer from scratch    — only if the interviewer is ML-facing
```

⭐ **The reframing matters more here than at any other company.** Amazon interviewers are not
compiler people; they are looking for *behaviour*. Tell the same project as an LP story.

**LP mapping for your actual experience:**

| Leadership Principle | Your story |
|---|---|
| **Dive Deep** | The points-to project — three bottlenecks found by profiling, not guessing ⭐ |
| **Ownership** | A year on the M.Tech project with a guide, not a manager; you set the direction |
| **Insist on the Highest Standards** | Exact-match validation against Soot on DaCapo; Wilcoxon testing in the IR project |
| **Bias for Action** | The SSSP project — brute force first, then the Near/Far redesign |
| **Learn and Be Curious** | ⭐ the Mechanical → CS switch. This is your strongest LP story |
| **Deliver Results** | 78% time reduction; 34.8 BLEU; 2M vertices / 300M edges |
| **Have Backbone** | ⚠️ weakest — needs a real story. See the STAR bank |
| **Earn Trust** | The TA role; precise attribution of PInter's work vs yours |

**Narrative risk most likely here:** no industry internship, and the behavioural depth.
→ `../../07_Interviews/04_HR_and_Behavioral/`

---

## 5. Tech stack and what to read

```
Java (heavily), Python, AWS everything (S3, DynamoDB, Lambda, EC2, SQS, Kinesis),
microservices, distributed systems at scale

READ:
  □ The 16 Leadership Principles — once, properly, before the OA  ⭐⭐⭐
  □ The Amazon Builders' Library ⭐ — excellent, short, and gives you something to reference
  □ One AWS service in depth if the role names it

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
OA format in depth ⭐ : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Amazon.md
                        (debugging section, Work Style, Work Simulation, partial scoring)
DSA patterns         : ../../01_DSA/pattern-index.md
STAR stories ⚠️       : ../../07_Interviews/04_HR_and_Behavioral/02-STAR_Story_Bank.md
LLD / HLD            : ../../04_System_Design/
Projects             : ../../07_Interviews/02_Project_Deep_Dives/
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
