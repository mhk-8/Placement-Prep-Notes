# How to Study This Folder

> A short operating manual. The content is deep enough that an unstructured read will not stick.

---

## 1. The three-pass method

**Pass 1 — orientation (one session per folder).** Read the section headings and every ⭐⭐⭐ block. Do not attempt the proofs. You are building a map of what exists.

**Pass 2 — derivation (several sessions per folder).** Work through every 📐 on paper. Close the notes and reproduce it. This is slow and it is the pass that actually creates capability.

**Pass 3 — recall (ongoing).** Flashcards on the D+1 / D+3 / D+7 / D+21 schedule from `00_Start_Here/Trackers/topic-revision-tracker.md`, plus the Q&A bank before every interview.

**Most people do pass 1 three times and call it studying.** The derivations are the difference.

---

## 2. The derivations you must own

These are the ones that come up repeatedly, listed by folder. Each should take under ten minutes on a blank page.

| # | Derivation | Folder |
|---|---|---|
| 1 | Normal equation for linear regression | `03` |
| 2 | Bias–variance decomposition | `03` |
| 3 | Logistic regression gradient (and why cross-entropy, not MSE) | `03` |
| 4 | Why L1 produces sparsity and L2 does not | `03` |
| 5 | Bagging variance reduction formula | `03` |
| 6 | SVM margin and the dual | `03` |
| 7 | PCA from the covariance eigendecomposition | `01` |
| 8 | Backpropagation for a two-layer network | `04` |
| 9 | Softmax + cross-entropy gradient simplifying to (ŷ − y) | `04` |
| 10 | Why attention scales by √d_k | `05` |
| 11 | MLE for Gaussian mean and variance | `02` |
| 12 | Bayes' theorem and the MAP/MLE relationship | `02` |

**Track them.** Tick each one only when you have reproduced it cold, twice, a week apart.

---

## 3. How to use the ⭐ markers

- **⭐⭐⭐** — these are the questions that appear in nearly every interview. Backprop, bias–variance, precision/recall trade-offs, overfitting diagnosis, transformer attention. Be able to answer each in two minutes, aloud, without notes.
- **⭐⭐** — expect these when the interviewer probes depth on something you claimed.
- **⭐** — breadth. Recognise and give a sentence.
- **⚠️** — read these twice. MCQ sections are built from exactly these misconceptions.

---

## 4. Connecting to your own projects

For every technique in this folder, ask: **did I use this, and could I defend the choice?**

An interviewer will take a claim from your resume and go three levels deep. "We used XGBoost" → "why not a neural network?" → "how did you tune it?" → "what did the learning curves show?". Each level filters.

**Build the answer chain while you study**, not the night before. `99_Interview_QA_Bank/project-questions.md` has the structure.

---

## 5. What to do when you do not know something

Say so, then reason from principles:

> "I haven't used that specific method, but from the name it should be doing X. The problem it would be solving is Y, and I'd expect the trade-off to be Z. Is that roughly right?"

This scores substantially better than bluffing, which is detected almost every time. **The ML interview rewards visible reasoning more than recall**, because that is what the job is.

---

## 6. Time budget

If you have four weeks for this folder, on top of everything else:

| Week | Focus |
|---|---|
| 1 | `01` + `02`, all derivations |
| 2 | `03` — the largest, and the highest interview yield |
| 3 | `04` + `05` |
| 4 | `08` + `09` + the Q&A bank; revise all derivations |

`06` and `07` are read-once unless your projects are in those areas.

If you have **one week**, do: bias–variance, metrics, regularisation, backprop, attention, leakage, and the scenario questions. That set covers most of what is asked.
