
# Defending Every Skill You Claim

> **The rule ⭐⭐⭐:** *do not list a skill you cannot answer three questions on.* Your resume lists
> roughly thirty distinct technologies. An interviewer will pick the one that looks least supported
> by your projects — that is the efficient thing for them to do — and a weak answer on a claimed
> skill damages you more than never claiming it would have.
>
> This file is the audit: for each claim, the evidence behind it, the three questions you should
> expect, and an honest risk rating.

---

## 1. The audit table ⭐⭐

| Claim | Evidence on your resume | Risk | Verdict |
|---|---|---|---|
| **C++** | Three CUDA projects; CS5800 TA | 🟢 Low | Strong — expect depth |
| **CUDA C++** | Three GPU projects; Nsight | 🟢 Low | Your differentiator |
| **Python** | Transformer, multi-task, IR, FFNN, Gesture | 🟢 Low | Strong |
| **PyTorch** | Transformer, multi-task vision | 🟢 Low | Strong |
| **NumPy** | FFNN from scratch (no autograd) | 🟢 Low | Strong — the from-scratch work proves it |
| **C** | Implied by CUDA C++ | 🟡 Medium | Expect pointer/memory questions ⚠️ |
| **Java** | Points-to analysis *analyses* Java; Soot | 🟠 **Higher** | ⚠️ See §3 — you analyse Java, you may not write it |
| **scikit-learn** | IR project (likely), Pattern Recognition course | 🟡 Medium | Know the API and 2-3 algorithms |
| **OpenCV** | Gesture Volume, image pipeline | 🟢 Low | Fine |
| **MediaPipe** | Gesture Volume | 🟢 Low | Library use, correctly framed |
| **spaCy / NLTK** | IR project preprocessing | 🟡 Medium | Know tokenisation, stemming vs lemmatisation |
| **Weights & Biases** | Three projects | 🟢 Low | Tool use, low risk |
| **Git/GitHub** | Universal | 🟢 Low | ⚠️ but see §4 — know more than add/commit/push |
| **Linux / WSL2** | Listed | 🟡 Medium | Expect basic shell and process questions |
| **Nsight Compute** | Points-to project | 🟠 **Higher** | ⚠️ "What did it tell you?" — have specifics |
| **Streamlit** | Gesture Volume (master resume) | 🟢 Low | Fine |

---

## 2. The three questions per major skill ⭐⭐⭐

### C++
```
1. "When do you need a virtual destructor, and what happens without one?"
2. "unique_ptr vs shared_ptr — when would you use each?"
3. "What is RAII and why does it matter?"
BONUS (systems roles): "Why does vector often beat list for middle insertion?"  ⭐ cache locality
```
Answers: `../01_Technical_Round_Prep/03-CS_Fundamentals_Rapid_Fire.md` §6.

### CUDA
```
1. "What is memory coalescing and how do you achieve it?"
2. "Explain warp divergence. When does it NOT matter?"
3. "How do you reduce atomic contention?"
BONUS: "Is higher occupancy always better?"  ⭐ the answer is no — see the GPU file §2(c)
```
Answers: `../01_Technical_Round_Prep/01-GPU_and_Parallel_Computing_QA.md`.

### Python
```
1. "List vs tuple vs set — when each, and what's the complexity?"
2. "What is the GIL and what does it mean for threading vs multiprocessing?"  ⚠️ near-certain
3. "Mutable default arguments — what's the trap?"   def f(x, lst=[]) ...
BONUS: "Generators vs lists — when and why?"
```

### PyTorch
```
1. "What does .backward() actually do? What is the computation graph?"
2. "Why model.eval() and torch.no_grad()? What breaks without each?"  ⭐
3. "How would you debug a model whose loss won't decrease?"
BONUS: "What does optimizer.zero_grad() do and what happens if you forget it?"
```

### NumPy
```
1. "Explain broadcasting rules."
2. "View vs copy — when does slicing give you which?"
3. "Why is a vectorised operation faster than a Python loop?"
```

### Machine learning generally
```
1. "Bias-variance trade-off."
2. "How do you detect overfitting, and what do you do about it?"
3. "Precision vs recall — when does each dominate?"
```
Answers: `../01_Technical_Round_Prep/04-ML_and_DL_Round_QA.md`.

---

## 3. The Java problem ⚠️⭐⭐⭐

**This is the sharpest gap in your skills list.**

Your resume lists **Java** among languages. But the evidence is that your points-to analysis
*analyses Java programs* and validates against **Soot**, a Java analysis framework. Analysing Java
bytecode is not the same as being a Java developer, and an interviewer who probes will find the
difference quickly.

```
THE RISK: a Java-heavy company (Oracle, many enterprise teams) may assume you are a Java
          developer and route you to a Java-specific round.

THE HONEST POSITION — say this if asked:
  "I should be precise about that. My M.Tech project analyses Java programs and validates
   against Soot, so I'm very comfortable with Java's semantics — the type system, how
   virtual dispatch works, what the bytecode looks like, why reflection is a soundness hole
   for static analysis. I've written Java, but I wouldn't claim it as my strongest language;
   C++ and Python are where I'm fastest."

WHY THIS WORKS: it is honest, and the thing you DO know (Java semantics at the bytecode and
type-system level) is genuinely unusual and arguably deeper than typical application-level
Java experience.  ⭐
```

**Your options:**
```
(a) Keep Java listed and prepare the honest framing above.       ⭐ RECOMMENDED
(b) Spend two weeks on Java fundamentals (collections, generics, concurrency, JVM memory
    model, GC) so the claim is solid. Worth it if you are targeting Oracle/enterprise.
(c) Move Java to the end of the list, or drop it from the SDE resume.
```

⚠️ Do **not** leave it listed prominently and unprepared. That is the combination that costs you.

---

## 4. The Nsight Compute problem ⚠️⭐⭐

Listing a profiler invites exactly one question: **"What did it tell you?"**

```
❌ WEAK : "I used it to profile the kernels and optimise them."
          → this is what someone who opened the tool once says

✅ STRONG: "The first thing I look at is whether the kernel is compute-bound or memory-bound —
           the memory throughput against the device peak answers that, and for this workload
           it was firmly memory-bound. Then warp execution efficiency for divergence, which is
           where the load imbalance showed up, and the L2 hit rate. For the propagation kernel
           specifically, the atomic throughput was the thing that flagged the contention
           problem I then fixed with per-block privatisation."
```

⭐ **Prepare one specific metric with a concrete finding.** If you genuinely did not record numbers,
say what you *looked at* and what you *concluded* — that is still a real answer. Vagueness is the
only failing response.

---

## 5. The Git problem ⚠️

"Git/GitHub" is on every resume, so it is rarely asked — but when it is, the question goes past
add/commit/push.

```
□ "merge vs rebase — when would you use each?"
□ "What is a detached HEAD and how do you get out of it?"
□ "How do you undo a commit that's already pushed?"  (revert, not reset —
   because rewriting shared history breaks everyone else) ⭐
□ "What is cherry-pick for?"
□ "How would you find which commit introduced a bug?"  (git bisect)
□ "Describe your branching workflow."
```

---

## 6. Skills you could credibly ADD ⭐

Based on what your projects actually involved but the resume does not claim:

```
+ Thrust          — you used thrust::sort_by_key and lower_bound in the SSSP project
+ Soot            — named in the master resume's project bullet but not in the skills line;
                    worth listing for compiler/static-analysis roles ⭐
+ DaCapo          — benchmark suite experience, relevant for performance roles
+ Static analysis / dataflow analysis — a CONCEPT worth listing under skills for compiler roles;
                    right now "compilers" appears nowhere in your skills line, which
                    under-sells your strongest project ⚠️⭐⭐
+ Multi-30k / standard NLP datasets — minor
```

⭐ **The biggest omission: your SDE skills line has no "Compilers / Static Analysis" category at
all**, even though your flagship project is exactly that. Adding a line like
`Compilers & Static Analysis: points-to analysis, dataflow analysis, Soot, LLVM basics` would make
the resume match the project.

---

## 7. Skills to be careful about ⚠️

```
⚠️ Do not add anything you have only read about. The cost of one exposed bluff exceeds the
   benefit of five extra keywords.
⚠️ Do not list a framework you used once through a tutorial.
⚠️ If you add "LLVM", be ready for LLVM IR questions. Only add it if you have actually used it.
⚠️ "Linux/WSL2" invites basic shell questions — ps, top, grep, pipes, permissions, signals.
   Know them.
```

---

## 8. The pre-interview skills drill (15 minutes) ⭐

```
 4 min : C++ — virtual destructor, RAII, smart pointers, move semantics
 4 min : CUDA — coalescing, divergence, occupancy, atomic contention
 3 min : Python/PyTorch — GIL, eval/no_grad, zero_grad, broadcasting
 2 min : Your Nsight answer, said aloud with a specific metric
 2 min : Your Java framing, said aloud
```

---

## Recall questions

1. State the rule for listing a skill.
2. What is the honest framing for your Java claim, and why is it actually strong?
3. What does listing a profiler oblige you to be able to say?
4. Why `revert` rather than `reset` for a pushed commit?
5. What category is missing from your SDE skills line, and why does it matter?
