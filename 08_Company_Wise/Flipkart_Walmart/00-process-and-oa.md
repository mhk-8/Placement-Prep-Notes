
# Flipkart and Walmart Global Tech — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> Grouped because the processes are similar (Walmart owns a majority stake in Flipkart and the
> engineering cultures overlap), the DSA band is the same, and both add a **machine-coding round**
> that most candidates have never practised.
>
> ⭐ **The machine-coding round is the differentiator at both.** Prepare it specifically; it is
> unlike anything in the rest of your preparation.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | SDE-1 (Flipkart), Software Engineer / SDE-3 track (Walmart) | ☐ |
| Eligibility | Usually no hard CGPA bar on campus | ☐ |
| Rounds | OA → 2-3 DSA rounds → **machine coding** → HM/HR | ☐ |
| OA platform | HackerRank / in-house | ☐ |
| Band | LeetCode Medium | ☐ |
| Machine coding | ⚠️ **60-120 min to design AND build a working system** — verify whether it runs | ☐ |
| Languages | Java, C++, Python; **Java common for machine coding** | ☐ |
| Locations | Bengaluru (both), Chennai (Walmart) | ☐ |

---

## 2. The process (typical shape)

```
OA (2-3 Medium coding) → DSA round ×2 → MACHINE CODING → Hiring manager → HR
```

| Round | What it actually tests |
|---|---|
| OA | Medium DSA; standard patterns |
| DSA rounds | Medium problems with follow-up variations; clean code |
| **Machine coding** ⭐ | Design *and implement* a small working system in 60-120 min: class design, extensibility, working code, tests |
| HM | Scale thinking, ownership, project depth |
| HR | Fit, relocation |

---

## 3. What they optimise for ⭐⭐⭐

> **Can you build something that works, cleanly, under time pressure?** The DSA rounds are
> standard; the machine-coding round is where candidates are separated, because it tests
> engineering rather than puzzle-solving — class design, separation of concerns, extensibility, and
> whether your code actually runs.

**The machine-coding round, specifically ⭐⭐⭐**
```
TYPICAL PROBLEMS (60-120 min, working code expected):
  - A parking lot system (the canonical one)
  - A library / book-lending system
  - Splitwise-style expense sharing
  - An elevator controller
  - A snake-and-ladder or chess move validator
  - An in-memory key-value store with TTL
  - A ride-matching / cab-booking system
  - A logging framework with pluggable sinks and levels

WHAT IS GRADED:
  1. WORKING CODE that compiles and runs ⚠️ this is the hard gate — an elegant design that
     does not run scores badly
  2. Class design — sensible entities, responsibilities, SOLID
  3. EXTENSIBILITY — "now add feature X" is always the follow-up, and it should be a small change
  4. No over-engineering — do not build a framework when three classes suffice
  5. Some tests, or at least a main() that demonstrates the flows
  6. Readability and naming

WHAT IS NOT GRADED:
  - A database (use in-memory collections)
  - A UI
  - Persistence, authentication, or a web layer — unless explicitly asked
```

**Topic emphasis, ranked:**
```
1. Machine coding / LLD ⭐⭐⭐ — the differentiator
2. DSA Medium — arrays, strings, hashing, trees, graphs, DP
3. OOP and SOLID; design patterns (strategy, factory, observer, decorator)
4. HLD basics for the HM round — scale, caching, sharding
5. Core CS fundamentals (lighter than Adobe/Oracle)
```

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐** |
| Why | Standard DSA band you can clear; good scale-oriented engineering questions |
| Biggest advantage | Your projects involve real engineering decisions with stated trade-offs (the Near/Far worklist, the multi-task loss balancing), which is exactly the reasoning the HM round probes |
| Biggest risk | ⚠️⚠️ **The machine-coding round.** Your projects are algorithmic and numerical, not object-oriented system builds. You have little practice designing a class hierarchy and shipping working code in 90 minutes |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
1. Parallel SSSP on GPU        — the engineering-decision story (Near/Far worklist, memory bound)
2. Points-to Analysis on GPU   — depth and ownership
3. Multi-Task Visual Perception — the trade-off discussion (loss balancing) if the interviewer
                                  is ML-facing
```

⚠️ **Do not lead with the compiler content here.** Frame projects around *engineering decisions and
trade-offs*, which is what this audience values.

**Narrative risk most likely here:** no industry internship (team-codebase experience is exactly
what the machine-coding round proxies for).

---

## 5. Tech stack and what to read

```
Java (heavily) ⚠️, Spring Boot, Kafka, Kubernetes, MySQL, Redis, Elasticsearch,
microservices at scale; Walmart adds significant Azure/GCP and supply-chain systems

⚠️ Java is the common machine-coding language. If you intend to use C++ or Python, CONFIRM
   it is permitted — and be aware that most published solutions and practice material are Java.

READ:
  □ Flipkart Tech Blog / Walmart Global Tech Blog — one recent post
  □ One LLD walkthrough (parking lot or Splitwise) end to end, including the code  ⭐

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
LLD / machine coding ⭐⭐⭐ : ../../04_System_Design/02_LLD_OOD/
                             (lld-framework, solid-in-practice, 5 case studies, machine-coding-guide)
Design patterns           : ../../04_System_Design/04_Design_Patterns/
HLD for the HM round      : ../../04_System_Design/03_HLD_Case_Studies/
DSA                       : ../../01_DSA/
OOP                       : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/OOPs.md
OA archetype              : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Startups_Unicorns_and_Others.md
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
