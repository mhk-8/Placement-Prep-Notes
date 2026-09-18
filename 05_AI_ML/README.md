# 05 — AI / ML

> **These are detailed teaching notes with full derivations, not revision cards.** Theorems are proved, architectures are drawn, and every topic marks what is actually asked in interviews.
> **Last filled: 2026-09-18.**

## The marking convention used throughout

| Marker | Meaning |
|---|---|
| ⭐⭐⭐ | **Asked in almost every ML interview.** If you know nothing else, know this |
| ⭐⭐ | Asked frequently; expect follow-ups |
| ⭐ | Asked occasionally, or as a depth probe |
| 📐 | A derivation you should be able to reproduce on a whiteboard |
| ⚠️ | A common misconception or a trap in MCQs |

Scan for ⭐⭐⭐ and 📐 first when time is short.

## Structure

| Folder | Contents | Priority |
|---|---|---|
| `01_Math_Foundations` | Linear algebra, matrix calculus, optimisation, PCA derived two ways | **P1** — IITM panels probe this hard |
| `02_Probability_and_Statistics` | Probability, distributions, inequalities with proofs, MLE/MAP, hypothesis testing, A/B design | **P1** |
| `03_Classical_ML` | Bias-variance (proved), regression, regularisation, trees, ensembles, SVM (dual derived), clustering, metrics | **P1 — the largest interview surface** |
| `04_Deep_Learning` | Backprop derived in full, activations, optimisers, normalisation, CNNs, RNN/LSTM | **P1** |
| `05_NLP_and_LLMs` | Embeddings, attention and transformers in full, BERT/GPT, fine-tuning, LoRA, RLHF, decoding, RAG, evaluation | **P1 — the 2026 differentiator** |
| `06_Computer_Vision` | Architectures, detection, segmentation, ViT, diffusion | P2 (P1 if your projects are vision) |
| `07_MLOps_and_Deployment` | Lifecycle, serving, optimisation, monitoring, drift | P2 |
| `08_ML_System_Design` | The framework plus five worked case studies | **P1 for ML roles** |
| `09_Data_Handling_and_Feature_Engineering` | numpy/pandas, cleaning, encoding, **leakage** | **P1** |
| `99_Interview_QA_Bank` | 60 conceptual Q&A, derivations to know, scenario questions, project questions | Read before every interview |

## How ML interviews are actually structured

Most ML interviews have **four** distinguishable parts, and candidates usually prepare only for the second:

1. **Coding** — still DSA, or a pandas/numpy manipulation task. `01_DSA` covers this; do not neglect it because you are on the ML track.
2. **ML theory** — definitions, derivations, "why does this work". This folder.
3. **Applied judgement** — "your model has 99% accuracy and is useless, why?" Scenario reasoning, in `99_Interview_QA_Bank/scenario-questions.md`.
4. **Your projects** — often 40–50% of the total time for an M.Tech candidate. `07_Interviews/02_Project_Deep_Dives` and `99_Interview_QA_Bank/project-questions.md`.

**Part 3 is where most candidates are weakest**, because it cannot be memorised. It is tested by scenarios, and the way to prepare is to reason through many of them.

## What separates a strong ML candidate

| Weak | Strong |
|---|---|
| "Random forest reduces overfitting" | "Bagging reduces variance by averaging decorrelated trees; feature subsampling lowers ρ in Var = ρσ² + (1−ρ)σ²/B" |
| Recites the bias-variance trade-off | Derives the decomposition |
| "We used accuracy" | "Accuracy is meaningless at 1% positive rate; we optimised PR-AUC and tuned the threshold on the business cost of a false negative" |
| Names an architecture | Explains what problem each component solves |
| "It worked well" | Quotes the metric, the baseline, and what failed first |

**The pattern: derive rather than recite, and attach every choice to a reason.**

## Reading order

1. `01_Math_Foundations` and `02_Probability_and_Statistics` — everything else stands on these
2. `03_Classical_ML` — the largest interview surface, and where bias-variance, metrics and regularisation live
3. `04_Deep_Learning` — backprop especially
4. `05_NLP_and_LLMs` — the current differentiator
5. `09_Data_Handling` — short, and leakage is asked constantly
6. `08_ML_System_Design` — after the rest
7. `06_Computer_Vision` and `07_MLOps` — as your projects require

## The rule for this folder

**Derive, do not memorise.** A derived result survives a follow-up question; a memorised one does not. Every 📐 marker is something to reproduce on paper, from a blank page, until it is automatic.
