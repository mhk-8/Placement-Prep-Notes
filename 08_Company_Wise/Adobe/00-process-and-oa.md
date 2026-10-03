
# Adobe — Process and OA Pattern

```
INFORMATION QUALITY : Low-Medium  — compiled from general knowledge, not from a 2026 campus notice
LAST VERIFIED       : not yet verified for this season
⚠️ RUN ../00-Research_Protocol.md BEFORE RELYING ON SECTION 1. The volatile table is a
   starting point to overwrite. What is durable here is sections 3-5.
```

> **⭐⭐ Low-effort, good-return target.** Adobe's signature is **C/C++ output-prediction MCQs**
> alongside Medium DSA and CS fundamentals — which is close to the cheapest marks available to you,
> because your daily language is C++ and your projects are memory-layout-heavy. Its graphics and
> imaging heritage also means matrix and geometry problems recur, which your image-pipeline project
> maps onto directly.

---

## 1. The volatile table ⚠️ — overwrite before applying

| Field | Value (starting point) | Verified |
|---|---|---|
| Role titles | Member of Technical Staff (MTS), Computer Scientist, Machine Learning Engineer | ☐ |
| Eligibility | Often a CGPA bar around 7.0 | ☐ |
| Rounds | OA → 2-3 technical → HM/HR | ☐ |
| OA platform | HackerRank / Adobe portal | ☐ |
| OA duration | ~90 min | ☐ |
| OA sections | Aptitude + **CS MCQ (OS, DBMS, OOP, C/C++ output)** + **2 coding problems** | ☐ |
| Languages | C, C++, Java, Python | ☐ |
| Locations | Noida, Bengaluru | ☐ |

---

## 2. The process (typical shape)

```
OA (aptitude + CS MCQ + 2 coding) → Technical ×2-3 → Hiring manager / HR
```

| Round | What it actually tests |
|---|---|
| OA | C/C++ output prediction, CS fundamentals, Medium DSA |
| Technical 1 | DSA + C/C++ depth |
| Technical 2 | CS fundamentals (OS, DBMS, OOP) + projects; sometimes LLD |
| HM / HR | Fit, motivation, communication |

---

## 3. What Adobe optimises for ⭐⭐⭐

> **Language-level precision.** Adobe wants people who know what their code *actually* does — not
> what they think it does. The output-prediction section is a deliberate filter for that, and it is
> unusual among product companies.

**Topic emphasis, ranked:**
```
1. C/C++ OUTPUT PREDICTION ⭐⭐⭐ — the Adobe signature (see §5 of the prep file)
2. DSA — Medium: arrays, strings, hash maps, trees, DP
3. OOP — inheritance, virtual functions, constructors/destructors order, SOLID
4. OS and DBMS fundamentals
5. Matrix and geometry problems ⭐ (imaging heritage): rotate, spiral, flood fill,
   overlapping rectangles, area calculations
6. Aptitude (light)
```

⚠️ **An honest note on output prediction:** many of these snippets are **undefined behaviour** in
the standard (`i = i++ + ++i` and relatives). Adobe still asks them with an "expected" answer from
a common compiler. Learn the common answer *and* know it is UB — mention the UB in an **interview**,
not in the MCQ.

---

## 4. Your fit ⭐⭐

| | Assessment |
|---|---|
| Fit | **⭐⭐, with the lowest marginal effort on your list** |
| Why | C++ is your daily language; your projects are pointer- and memory-layout-heavy; the matrix/geometry flavour matches your image pipeline |
| Biggest advantage | ⭐ The C/C++ section. Most candidates find it the hardest part of Adobe's OA; for you it is close to free marks |
| Biggest risk | The CS MCQ breadth (DBMS especially — your projects do not touch it), and DP ⚠️ |
| Positioning | Generalist SDE with systems depth |
| Resume | SDE version |

**Which projects to lead with:**
```
1. Image Preprocessing Pipeline on GPU  — ⭐⭐ lead with this at Adobe. Bilinear interpolation,
                                           align-corners coordinate mapping, memory layout,
                                           tolerance-based validation. It is an imaging project
                                           at an imaging company.
2. Multi-Task Visual Perception         — segmentation and localisation; vision-adjacent
3. Points-to Analysis on GPU            — the C++/systems depth story
```

⭐ **The line to use:** *"The image pipeline is the transform that runs before every
ImageNet-style inference — grayscale, bilinear resize, center crop, normalise — written as CUDA
kernels. The interesting part was the coordinate mapping: align-corners versus half-pixel centres
produce visibly different results, which is exactly the kind of mismatch that makes a model work in
training and fail in deployment."*

**Narrative risk most likely here:** CGPA, if they have a bar.

---

## 5. Tech stack and what to read

```
C++ (heavily), Java, JavaScript/React, Python, Adobe Sensei (ML), Creative Cloud services,
imaging and graphics pipelines, PDF internals

READ:
  □ Adobe Research publications ⭐ — strong imaging/vision work; pick one recent paper abstract
  □ Adobe Tech Blog
  □ Refresh: C++ undefined behaviour, operator precedence, object slicing, vtables

ONE THING TO REFERENCE:  ____________________________ ⭐
```

---

## 6. Cross-references

```
OA format in depth ⭐ : ../../06_Online_Assessments/01_OA_Patterns_by_Company/Adobe_Oracle_SAP_Salesforce.md
C/C++ gotchas ⭐⭐     : ../../03_Languages/CPP/gotchas.md
                        ../../07_Interviews/01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md §6
OOP MCQ bank         : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/OOPs.md
OS / DBMS banks      : ../../06_Online_Assessments/05_MCQ_Core_CS_Banks/
Aptitude             : ../../06_Online_Assessments/02_Aptitude_and_Quant/
Projects             : ../../07_Interviews/02_Project_Deep_Dives/03-Image_Preprocessing_Pipeline_GPU.md
```

---

## 7. Reservations and positives ⭐

```
POSITIVES:


RESERVATIONS:

```
