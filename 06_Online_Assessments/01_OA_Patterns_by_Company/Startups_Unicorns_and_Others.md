
# Startups, Unicorns and Everyone Else — Plus How to Research Any OA

> **Accuracy note.** Formats change every season. Use this file for the *method* of finding out a
> company's pattern, and the generic shapes that cover companies not given their own file.

---

## Part 1 — How to find out any company's OA pattern (do this every single time) ⭐⭐⭐

This is the **highest-return thirty minutes in the entire placement season**. Run it the moment a
company is announced.

```
1. PLACEMENT CELL  — ask for the JD, the test platform, the duration and last year's process note.
                     Many cells keep a repository. Ask explicitly; it is often not published.
2. SENIORS         — message 3-5 people from the last two batches who sat this company's test.
                     Ask these six questions:
                       • Which platform, and was it proctored/webcam?
                       • How many sections, how long, and did sections lock?
                       • Was there negative marking?
                       • What were the coding problems about (topic, not the exact statement)?
                       • What was the hardest section and what would you prepare differently?
                       • Roughly what score/attempt level got through?
3. PUBLIC ARCHIVES — GeeksforGeeks "company interview experiences", LeetCode Discuss company tag,
                     Glassdoor, the company's own careers page (which often describes the process).
4. THE PLATFORM    — if it is Codility/HackerRank/Mettl, do that platform's free demo test so the
                     interface is not new on test day. ⭐ Genuinely worth 20 minutes.
5. WRITE IT DOWN   — create `<Company>.md` in this folder before the test, using the template in
                     Part 3 below. Update it immediately after the test.
```

> **Asymmetry worth internalising:** knowing there is a locked 25-minute verbal section, or that
> the coding compiler is C++14, or that prompts must not be printed, is worth more marks than an
> extra week of practice.

---

## Part 2 — Generic OA archetypes

Almost every company's OA is one of five shapes. Identify which one, and you know how to prepare.

### Archetype A — "Volume filter"
*TCS, Infosys, Wipro, Accenture, Cognizant, Capgemini, LTIMindtree, Tech Mahindra, HCL, Mphasis,
Hexaware, Zensar, Virtusa, Deloitte (some tracks), Persistent*

```
Aptitude + logical + verbal + technical MCQ + 1-3 easy coding problems, sectionally locked
Prepare: arithmetic speed, reasoning patterns, basic coding fluency, pseudocode tracing
See: Infosys_Wipro_Accenture_Cognizant.md and TCS_NQT.md
```

### Archetype B — "DSA filter"
*Amazon, Microsoft, Flipkart, Walmart Global Tech, Uber, Atlassian, PhonePe, Swiggy, Zomato,
Sprinklr, Media.net, Arcesium (partly), Navi, CRED, Razorpay, Meesho, ShareChat, Zepto, Rubrik,
Nutanix, Juspay*

```
2-3 coding problems, 60-120 minutes, LeetCode Medium band, hidden tests with partial credit
Prepare: the 12 pattern families in 01_DSA/pattern-index.md; timed 2-problem sets
See: Amazon.md, Microsoft.md
```

### Archetype C — "DSA + fundamentals"
*Adobe, Oracle, SAP, Salesforce, Cisco, VMware, Dell, Qualcomm, Texas Instruments, Intuit, Zoho,
ServiceNow, NetApp, Siemens, Bosch, Samsung R&D, Philips, Honeywell*

```
Coding + a heavy CS-fundamentals MCQ section (OS, DBMS, CN, OOP, C/C++ output)
Prepare: 05_MCQ_Core_CS_Banks/ plus DSA Easy-Medium
See: Adobe_Oracle_SAP_Salesforce.md
```

### Archetype D — "Hard algorithmic"
*Google, Directi/Media.net (some rounds), Codenation/Trilogy, Sprinklr (senior), Tower, DE Shaw
(tech track), Uber (senior), Rubrik (senior)*

```
2 problems, Medium-Hard, observation-driven, constraint-signalled
Prepare: DP, graphs, binary search on answer, combinatorics; Kick Start archive
See: Google.md
```

### Archetype E — "Quant / probability"
*Optiver, Jane Street, Quadeye, Graviton, Tower Research, WorldQuant, DE Shaw (quant track),
AQR, Citadel Securities, IMC, Da Vinci, APT Portfolio*

```
Mental arithmetic under extreme time + probability + puzzles + estimation (+ sometimes hard DSA)
Prepare: daily arithmetic drill, probability and expected value, the puzzle canon
See: Goldman_Sachs_and_Quant_Firms.md
```

### Hardware / core-electronics variant
*Qualcomm, Texas Instruments, Nvidia, AMD, Intel, Micron, Analog Devices, Marvell, Synopsys,
Cadence, MediaTek*

```
Adds: digital logic, computer architecture, VLSI/verilog, signals, embedded C, RTOS
Prepare: 02_Core_CS/05_Computer_Architecture/ plus embedded C pointers/bit manipulation
```

---

## Part 3 — Template for a new company file

Copy this into `<Company>.md` when you research a new company, and fill it in.

```markdown
# <Company> — Online Assessment

Source of this information:  [placement cell / senior name / GFG link / own attempt], dated <date>
Confidence: [high = own attempt or this year's cell note | medium = senior, last year | low = public forum]

## Process
Stage 1: ...   Stage 2: ...   Stage 3: ...

## OA facts
| Field | Value |
|---|---|
| Platform | |
| Total duration | |
| Number of sections | |
| Sections lock? | |
| Negative marking? | |
| Webcam / proctoring | |
| Languages allowed | |
| Calculator allowed | |
| Partial scoring? | |

## Sections
| # | Section | Questions | Time | Notes |

## Topics seen
- ...

## Past questions (topic-level, from seniors/public sources)
- ...

## My attempt log
Date | Score | Sections attempted | What went wrong | Fix

## Post-test notes
(Write within one hour of the test: problems, approach, what you missed.)
```

---

## Part 4 — Notes on specific common campus recruiters

| Company | Shape | Things worth knowing |
|---|---|---|
| **Flipkart / Walmart** | B | LeetCode Medium; sometimes a machine-coding round later (design + code a working system in 90 min) |
| **Zoho** | C + unusual | Multi-round, full-day: aptitude → basic programming → **advanced programming on paper/laptop** → technical → HR. Heavy on implementation, light on exotic algorithms. Practise writing complete programs, not snippets ⭐ |
| **Samsung R&D (SRIB)** | D-ish | A single hard algorithmic problem in ~3 hours, often simulation/BFS/DP, with **C/C++ only** and no STL in some variants ⚠️ |
| **Qualcomm / TI / Nvidia** | Hardware | Digital logic, architecture, embedded C, bit manipulation, plus DSA |
| **Cisco / VMware / NetApp** | C | Networking and OS weighted heavily |
| **Sprinklr / Codenation / Media.net** | D | Genuinely hard; competitive-programming style |
| **Arcesium / DE Shaw (tech)** | C/D + quant flavour | DSA plus probability plus fundamentals |
| **JP Morgan / Morgan Stanley** | A/C hybrid | Aptitude + fundamentals + moderate coding; plus video/behavioural rounds |
| **Deloitte / PwC / EY / KPMG (tech)** | A | Aptitude-heavy, communication assessment, situational judgement |
| **PSUs via GATE** | Different | Selection by GATE score; no OA. Not covered here |

---

## Part 5 — The universal preparation core

Whatever the company, **80% of preparation is the same**:

```
1. DSA patterns (01_DSA/)                     — covers archetypes B, C, D
2. Arithmetic + reasoning speed (this folder) — covers archetype A and part of E
3. Core CS MCQ (05_MCQ_Core_CS_Banks/)        — covers archetype C and part of A
4. Probability (02_Probability_and_Statistics
   in 05_AI_ML/, plus this folder)            — covers archetype E
5. Timed practice discipline                  — covers all of them
```

Specialise in the last two weeks before a specific company. Do not specialise in month one.
