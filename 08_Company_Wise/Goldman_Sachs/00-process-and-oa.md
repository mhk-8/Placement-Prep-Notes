
# Goldman Sachs — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **⭐⭐ Moderate bar, distinctive process.** Goldman's technology division hires substantially from
> IITs. The coding bar is lower than Google's or Samsung's, but there are two things most
> candidates under-prepare: a **CS-fundamentals MCQ section** and a **recorded video (HireVue)
> round** that is scored.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | Analyst / Associate — Engineering; Core Engineering, Risk, Platform | ☐ |
| Eligibility | CGPA bar varies; often 7.0+ | ☐ |
| Rounds | OA → (**HireVue video**) → 2-3 technical → superday / HR | ☐ |
| OA platform | HackerRank | ☐ |
| OA sections | Numerical/aptitude · logical · **CS fundamentals MCQ** · **2 coding** · sometimes subjective | ☐ |
| Coding band | Easy to Medium | ☐ |
| HireVue? | ⚠️ **recorded video answers, ~30 s prep + 2-3 min each** — verify | ☐ |
| Locations | Bengaluru, Hyderabad | ☐ |

---

## 2. The process (typical shape)

```
OA (aptitude + logical + CS MCQ + 2 coding) → HireVue video → Technical ×2-3 → Superday / HR
```

| Round | What it actually tests |
|---|---|
| OA | Breadth: aptitude, reasoning, CS fundamentals, Easy-Medium coding |
| HireVue | Recorded behavioural answers. **Scored.** Practise on camera ⚠️ |
| Technical 1-2 | DSA (Easy-Medium), CS fundamentals, projects |
| Technical 3 / superday | Depth, fit, sometimes a light design or domain question |
| HR | Motivation for finance, long-term plans |

---

## 3. What Goldman optimises for ⭐⭐⭐

> **Breadth and communication, more than algorithmic peak.** Goldman is hiring engineers who will
> work with non-engineers on systems where correctness has financial consequences. The coding bar
> is reachable; the differentiators are fundamentals breadth and the ability to explain yourself
> clearly on camera and in person.

**Topic emphasis, ranked:**
```
1. CS fundamentals breadth — data structures and complexity, OS, DBMS, networks, OOP,
   and basic security (hashing vs encryption, symmetric vs asymmetric) ⭐
2. DSA — Easy to Medium. Arrays, strings, hash maps, sorting, greedy, simple DP
3. Aptitude and logical reasoning (a real section — do not ignore it)
4. Communication — the HireVue round and the interviews both weight it
5. Financial-flavoured problem wrappers ⭐ (see below)
6. "Why finance?" — a genuine, prepared answer
```

⭐ **The financial wrappers to recognise:** stock buy/sell problems in all five variants
(one transaction, unlimited, at most k, with cooldown, with fee), portfolio balancing, order
matching, interest and compounding computations. The patterns are standard; the story is financial.

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐** |
| Why | The coding bar is comfortably within reach; your CS fundamentals are solid and your TA role is credible evidence of communication |
| Biggest advantage | ⭐ **The TA role.** Goldman weights communication heavily, and "I TA the Advanced DS&A course" is concrete evidence rather than a claim |
| Biggest risk | ⚠️ **The HireVue video round** — unpractised, and it is scored. Plus "why finance?", which you have no natural answer to yet |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
1. Information Retrieval Engine — ⭐ the statistical-rigour story lands well in finance:
                                   "a better number is not a real improvement"
2. Points-to Analysis on GPU    — depth and ownership; frame it as correctness-critical work
3. Parallel SSSP on GPU         — algorithms, with concrete numbers
```

**"Why finance?" — prepare this properly ⚠️⭐⭐**

You have no finance background, so a generic answer will be obvious. The honest, defensible version:
> "I'm drawn to the constraint rather than the industry. In finance a wrong number has an immediate
> cost, which means the engineering standard for correctness is higher than in most software — you
> cannot ship something that is approximately right. That matches how I've worked: my M.Tech
> project validates exactly against a reference implementation because a soundness-critical
> analysis cannot be mostly correct, and in my IR project I ran significance testing rather than
> just reporting a better number. That instinct seems more valuable here than in most places."

⭐ That answer uses evidence you actually have, rather than claiming an interest in markets you do
not have. Do not pretend to follow finance if you do not.

**Narrative risk most likely here:** "why finance?", then the CGPA.

---

## 5. Tech stack and what to read

```
Java (heavily), Python, Slang/SecDB ⭐ (Goldman's proprietary platform — worth knowing it exists),
kdb+, Kafka, Kubernetes, large-scale risk and pricing systems

READ:
  □ What SecDB is, in one paragraph ⭐ — mentioning it credibly signals genuine research
  □ Goldman Sachs Engineering blog / GS Developer — one recent post
  □ Basic market vocabulary: equities vs derivatives, settlement, risk — a paragraph each is enough

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
OA format in depth ⭐ : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Goldman_Sachs_and_Quant_Firms.md
Core CS MCQ banks    : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/
Aptitude             : ../../06_Online_Assessments/02_Aptitude_and_Quant/
STAR stories (video) : ../../07_Interviews/04_HR_and_Behavioral/02-STAR_Story_Bank.md
Projects             : ../../07_Interviews/02_Project_Deep_Dives/06-Information_Retrieval_Engine.md
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
