
# Resume Audit — Your Three Versions

> **Analysed from the PDFs you provided:** `OLD Master Resume.pdf` (2 pages), `SDE Resume.pdf`
> (1 page), `ML Resume.pdf` (1 page). This is an honest audit, not reassurance — the point is to
> find what an interviewer will notice before they notice it.

---

## 1. What you have ⭐

| Version | Pages | Experience section | Projects | Skills emphasis |
|---|---|---|---|---|
| **Master** | 2 | Infosys Springboard (3 bullets) | 6 (Points-to, SSSP, Image GPU, Transformer, Multi-task, FFNN, IR) | Broad, undifferentiated |
| **SDE** | 1 | **None** ⚠️ | 5 (Points-to, SSSP, Image GPU, IR, Transformer) | C++, CUDA, GPU & parallel computing |
| **ML** | 1 | Infosys Springboard (2 bullets) | 4 (Points-to, Transformer, Multi-task, IR) | Python first; ML/DL & NLP |

**The strategy is sound.** Two targeted one-pagers plus a master from which to cut is exactly right,
and the differentiation between them is real (ordering, skills line, which projects survive) rather
than cosmetic. Most candidates send the same resume everywhere; you do not.

---

## 2. What is genuinely strong ⭐⭐⭐

```
1. QUANTIFIED EVERYWHERE. 96%, 78%, 34.8 BLEU, 0.91 macro-F1, 0.87 macro-Dice, +22.8%,
   MAP 0.2691 → 0.3304, 2M vertices / 300M edges. Almost every bullet has a number.
   This is rarer than you think and it is the first thing a good reviewer looks for.

2. THE RARE COMBINATION. CUDA + compilers + ML. Most candidates have one. You have all three,
   and the M.Tech project sits exactly at the intersection. For systems/HPC/ML-infra roles
   this is a genuine differentiator, not a claim.

3. STATISTICAL RIGOUR. "Wilcoxon signed-rank, p = 3.3e-16, Cohen's d = 0.57" on a course
   project is unusual. It signals someone who knows the difference between a better number
   and a real improvement.  ⭐

4. HONEST NUMBERS. The master resume reports 50% localisation accuracy at IoU ≥ 0.75 — a weak
   result, included anyway. Reviewers who notice this trust the rest of the document more.

5. TA POSITIONS. CS5800 Advanced DS&A and CS1111 — teaching DSA is directly relevant evidence
   for a DSA round, and almost nobody uses it. Use it. ⭐

6. FORMAT. Clean, one page for the targeted versions, reverse chronological, consistent.
```

---

## 3. What an interviewer will notice — the honest list ⚠️⭐⭐⭐

### (a) The SDE resume has **no experience section at all**
Your SDE version drops the Infosys internship entirely. That is a defensible choice for space, but
the consequence is a resume with **zero work experience of any kind**. A recruiter scanning for
"has this person worked?" gets no signal.

```
OPTIONS:
  (i)  Add the Infosys line back as a single compressed bullet. Cost: ~2 lines.
  (ii) Add the TA positions UP into a "Experience" or "Teaching Experience" section rather
       than leaving them at the very bottom under "Positions of Responsibility".  ⭐ cheap and
       effective — TA-ing an Advanced DS&A course IS relevant work.
  (iii) Leave as is and handle it verbally (see ../04_HR_and_Behavioral/04-Difficult_Questions.md)

RECOMMENDATION: (ii). Retitle the section and move it up. It costs nothing and it converts
"no experience" into "teaching experience in the exact subject you're testing me on".
```

### (b) The B.Tech in **Mechanical Engineering** is unexplained
It is in the education table and nowhere else. Every single interviewer will ask about it, and the
resume gives them no help. The resume cannot tell the story — but you can decide whether it *invites*
the question neutrally or awkwardly.

```
This is handled verbally, not on the resume. See:
  ../04_HR_and_Behavioral/01-The_Mechanical_to_CS_Story.md   ⭐⭐⭐ the most important file for you
```
⚠️ Do **not** add a "Career Objective" paragraph trying to explain it. Objectives are dead weight
and it would draw more attention, not less.

### (c) CGPA 7.5 at M.Tech, **below** your B.Tech 7.95
A reviewer scanning the education table sees the trend go down. Some companies have a hard 7.0 or
7.5 cut-off, so you clear those — but the direction invites a question.

```
Do not hide it, do not explain it on the resume. Prepare the verbal answer:
  ../04_HR_and_Behavioral/04-Difficult_Questions.md §2
Short version: an M.Tech CSE at IIT Madras after a Mechanical B.Tech means the first two
semesters were spent absorbing four years of CS fundamentals simultaneously. That is a real
and creditable reason, said calmly and without defensiveness.
```

### (d) Dates: your projects are dense in a short window
Apr 2026 appears on two projects, Mar 2026 on one, Feb 2026 on one. A reader may wonder how deep
each went. This is normal for coursework — but be ready for *"these look like course assignments"*,
because they are, and the answer is that the **depth** (from-scratch implementations, statistical
testing, profiling) is what distinguishes them from typical coursework.

### (e) No links rendered as text
`LinkedIn` and `GitHub` appear as icons/links. ⚠️ If the PDF is parsed by an ATS or printed, an
icon without visible URL text may lose the link entirely.

```
FIX: render them as visible text — "github.com/<username>" — not just an icon.
     Same for the LinkedIn URL. This is a 2-minute fix with real downside risk if skipped.  ⭐
```

### (f) No GitHub evidence for the strongest claims
Your best projects are the GPU ones. If those repositories are private, empty or un-READMEd, the
most impressive claims on the resume are unverifiable. See `02-GitHub_and_LinkedIn_Checklist.md`.

### (g) The ML resume leads with a GPU compiler project
On the ML resume, the first project a reader sees is "Constraint-Based Points-to Analysis on GPU".
For a *pure* ML role that is a mismatched opening.

```
OPTIONS:
  (i)  Reorder so the Transformer leads on the ML resume, with Points-to third or fourth.
       ⭐ RECOMMENDED for pure ML/DS roles.
  (ii) Keep it first for ML-INFRASTRUCTURE roles (NVIDIA, inference teams, ML systems),
       where it is an asset, not a detour.

You may want a third variant: ML_Infra_Resume.pdf, which keeps the GPU work first and adds
the SSSP/Image projects back. That is a real and under-served role category for your profile.
```

### (h) "(cid:123)" artefacts in the PDF text layer
When your PDF's text is extracted, the bullet glyphs come out as `(cid:123)` and `(cid:239)`. This
means the bullet font is not mapped to Unicode.

```
⚠️ WHY IT MATTERS: an ATS parsing your resume may produce garbage at the start of every bullet,
   or may fail to segment bullets at all.
FIX: in your LaTeX template, use a standard bullet character rather than a font-specific glyph
   (e.g. \item with the default, or \textbullet), or ensure font encoding is set so glyphs map
   to Unicode (\usepackage[T1]{fontenc} and a Unicode-mapped font).
   Test: open the PDF, Ctrl+A, copy, paste into a text editor. If you see (cid:xxx), fix it. ⭐⭐
```
This is the single most actionable technical fix in this audit.

---

## 4. Line-level suggestions ⭐

| Current | Issue | Suggested |
|---|---|---|
| "Parallelizing PInter (CC'26) on NVIDIA GPUs" | A reader unfamiliar with PInter learns nothing from the name | "Parallelizing PInter (CC'26), an Andersen-style points-to analysis for Java, on NVIDIA GPUs" — you already do this; keep it and never cut it for space |
| "cutting constraint count by 96% and analysis time by 78% over the traditional scheme" | ⚠️ Ambiguous attribution — is that PInter's result or yours? | Consider: "...over the traditional scheme; my contribution is the CUDA parallelisation." Or leave it and handle verbally — but **be scrupulous in the interview** (see project file §11) |
| "achieved 0.91 macro-F1 on classification and 0.87 macro-Dice on segmentation" (ML resume) | The ML resume drops the 50% localisation number that the master keeps | Fine — one-pagers must cut. But **know the number** and volunteer it if asked, so it never looks like concealment |
| "INFOSYS Springboard — Virtual Intern" | Correctly labelled "Virtual" ✅ | Keep the word "Virtual". Removing it would be the dishonest move |
| Coursework lines | Long and dense | Consider trimming to the 4-5 most relevant per version. On the SDE resume, "Pattern Recognition" earns less than "Advanced Programming Lab" |
| "Positions of Responsibility" | The aero/design club roles from B.Tech only appear on the master | Correct call. On a 1-pager, TA positions matter, clubs do not |

---

## 5. What is missing that would strengthen it ⭐⭐

```
□ A visible GitHub URL as TEXT (§3e) — highest priority, 2 minutes
□ Fix the (cid:) bullet encoding (§3h) — highest technical priority
□ TA positions moved into an "Experience" heading on the SDE resume (§3a)
□ One line naming the SCALE of the points-to work — how many constraints, which benchmarks?
  "validated on DaCapo" is there; adding "over N constraints" would land harder
□ Consider a third variant for ML-INFRASTRUCTURE roles (§3g)
```

**What NOT to add ⚠️:**
```
✗ A "Career Objective" or "Summary" paragraph — dead space on a student resume
✗ A skills rating bar chart (★★★☆☆) — it invites "why is Java 3 stars?" and ATS cannot read it
✗ Photographs, date of birth, marital status, full address — not conventional for tech roles,
  and they consume the space your projects need
✗ Soft skills as a list ("team player, quick learner") — unverifiable and universally ignored
✗ Hobbies, unless genuinely conversation-worthy
```

---

## 6. Version control for your resumes ⭐

Keep the discipline the vault README already recommends:
```
03_Resume_and_Portfolio/
  ├── versions/
  │     ├── 2026-09_SDE_v3.pdf
  │     ├── 2026-09_ML_v3.pdf
  │     ├── 2026-09_Master_v5.pdf
  │     └── CHANGELOG.md
  └── (this folder's notes)
```

**CHANGELOG.md format:**
```markdown
## 2026-09-22 — v3
- Rendered GitHub/LinkedIn URLs as visible text (ATS link loss)
- Fixed bullet glyph encoding (cid: artefacts in text layer)
- SDE: renamed "Positions of Responsibility" → "Teaching Experience", moved above Projects
- ML: reordered projects, Transformer first
- Sent to: <company>, <company>
```
⭐ **Why it matters:** when an interviewer asks about a bullet, you need to know which version they
are reading. Tracking what you sent where prevents the moment where you describe a project that is
not on their copy.

---

## 7. The priority order ⭐⭐⭐

If you do only three things:

```
1. FIX THE (cid:) BULLET ENCODING.  Real ATS risk, 10-minute fix.                    §3h
2. MAKE THE GITHUB URL VISIBLE TEXT, and make sure the repos behind it are presentable. §3e, §3f
3. RETITLE AND PROMOTE THE TA SECTION on the SDE resume.                              §3a
```

Everything else in this file is optimisation. Those three are defect fixes.

---

## Recall questions

1. What does the SDE resume lack entirely, and what is the cheapest fix?
2. What is the `(cid:123)` problem and why does it matter?
3. Which project leads the ML resume, and when is that right versus wrong?
4. What should you *not* add to the resume, and why?
5. Why keep the word "Virtual" in the Infosys line?
