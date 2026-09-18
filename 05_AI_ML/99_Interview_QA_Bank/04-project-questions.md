
# Talking About Your Projects ⭐⭐⭐

> This is the part of the interview candidates under-prepare and interviewers weight most heavily.
> A technically weaker candidate who can explain one project with real depth usually beats a
> stronger one who recites a pipeline.

---

## 1. The structure for every project ⭐⭐⭐

```
CONTEXT   : the problem, why it mattered, who the user was          (20 s)
DATA      : source, size, labels, the messy part                     (20 s)
APPROACH  : baseline → what you tried → what you chose and WHY       (40 s)
RESULT    : a number, against a baseline, with the metric named      (20 s)
LEARNING  : what you would do differently; what surprised you        (20 s)
```

Two minutes, then stop and let them dig. ⚠️ Rambling for six minutes without a result is the most
common failure.

**The single most important word is "why".** "I used XGBoost" is worth nothing; "I used XGBoost
because the features were heterogeneous tabular data with strong interactions, and it beat my
logistic-regression baseline by 6 points of PR-AUC while training in two minutes" is worth a lot.

---

## 2. The questions you will be asked ⭐⭐

```
□ Why this approach and not <the obvious alternative>?
□ What was your baseline, and by how much did you beat it?
□ Which metric, and why that one? What was the cost of each error type?
□ How did you split the data? Why that way?
□ How did you know it was not overfitting or leaking?
□ What was the hardest bug, and how did you find it?
□ What did NOT work, and why?
□ If you had another month, what would you do?
□ How would you deploy this and what would break first?
□ What would you do differently knowing what you know now?
□ Can you draw the system on the whiteboard?
□ What was YOUR contribution versus the team's? ⚠️ be precise and honest
```

⭐ Prepare an honest answer to "what did not work". Candidates who claim everything worked on the
first attempt sound like they did not really do the work.

---

## 3. Depth traps to prepare for ⚠️⭐⭐

Interviewers probe one level deeper than your comfort. For every project, be ready for:

```
"You said you used BERT — what exactly does the [CLS] token represent, and did you finetune
 it or use it frozen?"
"You said you used cross-validation — was your preprocessing inside the folds?"
"You said accuracy improved — what was the class balance?"
"You said you used SMOTE — where in the pipeline, and what did that do to your
 probability calibration?"
"You said the model got 0.95 AUC — what did the confusion matrix look like at the
 threshold you would actually deploy?"
"You said you tuned hyperparameters — on which data, and how did you avoid
 overfitting the validation set?"
```

If you do not know, say so and say what you would check. Bluffing is far worse than a clean "I did
not measure that; here is how I would."

---

## 4. Turning a course or hobby project into a strong story ⭐⭐

Most student projects are "I trained a model on a Kaggle dataset". Make it interviewable by adding
one dimension of realism:

| Add | Example |
|---|---|
| A baseline and an ablation | "logistic regression got 0.71; each added feature group is worth X" |
| An error analysis | "40% of errors were one confusable class pair; here is why" |
| A deployment | "wrapped in FastAPI, 40 ms p95, containerised, with a fallback" |
| A leakage or data bug you found | "the original split leaked patients; correcting it dropped AUC from 0.94 to 0.86 — and the honest number is the useful one" ⭐⭐ |
| A cost or latency constraint | "quantised to int8 to fit 100 MB on device, losing 0.4 points" |
| A monitoring plan | "I would track PSI on the top 10 features and refusal rate weekly" |

⭐ **The leakage story is gold.** Finding and fixing a leak — and reporting the *lower* honest number
— demonstrates exactly the judgement interviewers are screening for.

---

## 5. Research and paper-reading questions ⭐

```
"What's the most interesting paper you've read recently?"
```

Have two ready, at least one from the last twelve months. For each: the problem, the key idea in one
sentence, why it mattered, and one honest limitation or criticism. Being able to critique it is what
distinguishes reading from skimming.

```
"How do you keep up with the field?"
```

Name specifics: particular venues, a newsletter, arXiv areas, reimplementing a paper, following
specific labs' releases. Vague answers read as "I don't".

---

## 6. Behavioural questions with an ML flavour ⭐⭐

Use **STAR** (Situation, Task, Action, Result) and always land a concrete result.

```
"Tell me about a time you disagreed with a teammate."
"Tell me about a project that failed."      ⭐ have a real one, with what you learned
"How do you prioritise when everything is urgent?"
"Tell me about explaining something technical to a non-technical stakeholder."
"How do you handle an ambiguous problem statement?"
"Tell me about a time you were wrong."
```

⚠️ Prepare four or five distinct stories that you can re-cut to fit several questions, rather than
one story you force everywhere.

---

## 7. Your questions for them ⭐

Always ask two or three. Good ones:

```
"How is success measured for this role in the first six months?"
"What does the path from a model in a notebook to production look like here?"
"How much of the work is modelling versus data and infrastructure?"
"How do you decide what to work on — is it research-driven or product-driven?"
"What is the biggest technical debt in the current ML stack?"
"How are models monitored and retrained today?"
```

⚠️ Do not ask anything answered on the careers page; and avoid making compensation the first
question in a technical round.

---

## 8. The one-page project sheet ⭐⭐⭐

Write this for each of your top three projects and rehearse it aloud until it is fluent.

```
PROJECT:
One-line pitch:
Problem and why it mattered:
Data (source, size, labels, the messy part):
Baseline and its score:
Approach chosen and WHY (with the alternative you rejected and why):
Metric and why that metric:
Split strategy and why:
Result vs baseline:
Hardest bug and how you found it:
What did not work:
What you would do with another month:
How you would deploy it and what would break first:
Your specific contribution:
```

⚠️ Rehearse **out loud**, timed. Reading it silently creates an illusion of fluency that collapses
under a whiteboard and a stranger's gaze.
