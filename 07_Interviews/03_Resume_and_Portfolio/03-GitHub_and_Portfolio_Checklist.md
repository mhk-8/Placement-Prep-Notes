
# GitHub, LinkedIn and Portfolio Checklist

> **Why this matters for you specifically ⚠️⭐⭐⭐.** Your strongest resume claims are the GPU
> projects, and they are also the least verifiable ones. If a systems interviewer at NVIDIA or a
> compiler team looks up your GitHub and finds an empty profile, the claims do not disappear — but
> they stop being *evidence* and become *assertions*. For a candidate whose differentiator is
> unusual technical depth, a presentable repository is worth more than an extra project.

---

## 1. The GitHub audit ⭐⭐⭐

```
□ Profile README exists (a repo named after your username)
□ Profile photo, a one-line bio naming what you actually work on, location, email or link
□ Pinned repositories: exactly the 4-6 that match your resume  ⭐
□ No forked repos, tutorial clones or half-finished experiments sitting on top
□ Every pinned repo has a real README (§2)
□ Commit history is not one giant "initial commit" dump for a semester's work ⚠️
□ Repos are PUBLIC (or you have a clear answer for why not — see §5)
□ No secrets, API keys, or large binary blobs in history
□ A LICENSE on anything you would want someone to reuse
```

**The pinned set, given your resume:**
```
1. points-to-analysis-gpu     ⭐ flagship (if publishable — see §5)
2. delta-stepping-sssp-cuda   ⭐ strongest publishable systems project
3. transformer-from-scratch   ⭐ strongest ML project
4. multitask-vision-pipeline
5. ir-engine-cranfield        (the statistical rigour is visible in the README)
6. neural-net-from-scratch    (proof of fundamentals)
```

---

## 2. The README that actually gets read ⭐⭐⭐

A recruiter or engineer spends **under 60 seconds** on a repository. Structure for that.

```markdown
# Δ-Stepping SSSP on GPU

One-line description: a CUDA implementation of Δ-stepping single-source shortest path
over CSR graphs, sustaining 2M vertices / 300M edges on a single allocation.

## Result
| Metric | Value |
|---|---|
| Max graph size | 2M vertices, 300M edges (~2.6 GB) |
| Source queries | up to 20 |
| Baseline compared against | <CPU Dijkstra / reference> |
| Speedup | <number> |

## Why Δ-stepping
Dijkstra is inherently sequential; Bellman-Ford is parallel but wasteful. Δ-stepping
buckets vertices by tentative distance and processes a bucket in parallel, with Δ as
the dial between the two.

## Key design decisions
- **Near/Far two-queue worklist** instead of per-bucket arrays → O(|V|) memory,
  independent of graph diameter
- **State mask** blocking duplicate enqueues during parallel relaxation
- **Adaptive Δ**, retuned each round via thrust::sort_by_key, targeting ~65k active vertices

## Build and run
```bash
make
./sssp graph.txt --sources 20 --adaptive
```

## Repository structure
src/ ... include/ ... benchmarks/ ...

## Limitations
Non-negative weights only. Single GPU. Weakest on high-diameter graphs where the
frontier is narrow.
```

⭐ **The two sections that distinguish a good README:** *"Key design decisions"* (shows judgement,
not just implementation) and *"Limitations"* (shows honesty). Both are the same instincts that make
a good interview answer.

⚠️ **Do not** write a README that is just build instructions. That tells a reader nothing about
whether you can think.

---

## 3. Commit history hygiene ⭐⭐

An interviewer who is genuinely interested **will** scroll your commits.

```
❌ BAD : one commit, "final code", 4,000 lines, dated the night before submission
✅ GOOD: 30-60 commits over the project's life, with messages that describe the change

Good messages:
  "Replace per-bucket arrays with Near/Far two-queue worklist"
  "Add state mask to prevent duplicate enqueues in parallel relax"
  "Fix: adaptive delta was splitting equal distances across rounds"
❌ "update", "fix", "asdf", "final v2 FINAL"
```

⚠️ **If your history is already a single dump, do not fake it** by rewriting history into invented
commits. Instead: make the *ongoing* work (the M.Tech project) well-committed from now on, and let
the older repos be what they are. A faked history is discoverable and unrecoverable.

---

## 4. The one thing that would most strengthen your portfolio ⭐⭐⭐

**A short technical write-up of the points-to GPU work.** Not a paper — a blog post or a long
README, 1,000-1,500 words, with a diagram.

```
Why it is worth more than another project:
  - It makes your least-legible project legible to a non-compiler reader — which is exactly
    the risk identified in ../02_Project_Deep_Dives/01-Points_To_Analysis_on_GPU.md
  - It demonstrates technical communication, which is scored in every senior interview
  - It gives you something to link in an application and to refer to in an interview
  - Writing it forces you to find the gaps in your own understanding  ⭐

Structure:
  1. What points-to analysis is and why anyone cares (for a general reader)
  2. Why it's a bad fit for a GPU, and why that makes it interesting
  3. The data-structure mapping, with a diagram
  4. The three bottlenecks and the fixes
  5. Results and honest limitations
```

Host it on GitHub Pages, a personal site, or Medium. Link it from your GitHub profile README and
your LinkedIn.

---

## 5. What if the M.Tech code cannot be public? ⚠️

Research code under a guide, building on unpublished or in-submission work, often cannot be shared.
That is normal and it is not a problem — but handle it explicitly.

```
✅ Create a repository named points-to-analysis-gpu containing ONLY a README:
     - the problem, the approach, the results
     - a diagram of the architecture
     - "Source code is not public as this work is ongoing under Prof. V. Krishna Nandivada
        at IIT Madras. Happy to discuss the design in detail."

This gives a reader everything except the code, and it signals that you understand
academic norms rather than that you have nothing to show.  ⭐
```
⚠️ Check with your guide before publishing **any** detail of unpublished work, including the
README. Ask; do not assume.

---

## 6. LinkedIn ⭐

```
□ Headline is specific, not generic
     ❌ "M.Tech student at IIT Madras | Passionate about technology"
     ✅ "M.Tech CSE @ IIT Madras | GPU & parallel systems, compilers | CUDA, C++"   ⭐
□ About section: 3-4 sentences, the same content as your 30-second introduction
□ Education and dates match the resume EXACTLY ⚠️ (recruiters do check)
□ Projects section mirrors the resume's top 3-4, with links
□ Experience: the Infosys internship, labelled "Virtual Internship" as on the resume
□ TA positions listed under Experience  ⭐ (same argument as the resume audit)
□ Skills endorsed; the top three should be C++, CUDA and Python, in that order for SDE
□ Open to work, set to recruiters only, if you want inbound
□ Connect with seniors from your batch and the one above — they are your best source of
  company-specific interview information
```

---

## 7. The consistency check ⭐⭐

⚠️ Recruiters cross-reference. Make these identical across resume, LinkedIn and GitHub:

```
□ Your name, spelled and capitalised the same way
□ Degree titles and institution names
□ Dates — start and end months, exactly
□ CGPA, if stated anywhere
□ Project titles
□ The Infosys internship's dates, title and the word "Virtual"
```

A mismatch is rarely fatal by itself, but it is the kind of small inconsistency that makes a
reviewer start checking other things.

---

## 8. Before an application, 20 minutes ⭐

```
□ Open your GitHub as a logged-out user. What does a stranger see in 30 seconds?
□ Check the pinned repos are the right ones for THIS company
     (systems company → GPU projects pinned first; ML company → reorder)  ⭐
□ Check every pinned repo's README renders correctly and has no broken links
□ Check your resume PDF: Ctrl+A, copy, paste into a text editor. Any (cid:) artefacts?
     → see 00-Resume_Versions_Audit.md §3h
□ Check the GitHub and LinkedIn URLs on the resume are CLICKABLE and VISIBLE AS TEXT
□ Check LinkedIn matches the version of the resume you are sending
```

---

## 9. The priority order ⭐⭐⭐

If you do only three things:

```
1. WRITE PROPER READMEs for the three projects you pin. Design decisions + limitations.
2. WRITE THE POINTS-TO WRITE-UP (§4). It is the highest-leverage single item available
   to you, because it fixes your flagship project's legibility problem.
3. MAKE THE RESUME LINKS VISIBLE AND WORKING, and check what a logged-out visitor sees.
```

---

## Recall questions

1. How long does a reader spend on a repository, and what must be in the first screen?
2. Which two README sections distinguish a good repo from a build-instructions repo?
3. What should you do if your M.Tech code cannot be public?
4. What should you never do to a commit history, and why?
5. Name the single highest-leverage portfolio item for your profile, and why.
