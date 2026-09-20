
# TCS NQT (National Qualifier Test)

> **Accuracy note.** OA formats change every recruiting season, and companies run different
> papers for different campuses, roles and dates. Treat everything below as the *shape* of the
> test — what it measures and how to prepare — and verify the exact duration, section count and
> cutoff against this year's campus notice and last year's seniors before you sit the test.


---

## 1. What the NQT is

TCS NQT is a **common qualifier** used for multiple TCS hiring tracks. Your score determines which
band you are considered for. This is the single most important structural fact about it:

```
                    TCS NQT (common test)
                            │
        ┌───────────────────┼────────────────────┐
        ▼                   ▼                    ▼
    Ninja / Digital     Digital / Prime      Prime (highest band)
    (base package)      (higher package)     (advanced round required)
```

Higher bands require **additional advanced sections** (harder coding, advanced quant/reasoning).
Aim for the advanced sections even if you are unsure — they are the difference between bands.

---

## 2. Structure (Foundation + Advanced pattern)

| Section | Approx. questions | Approx. time | Notes |
|---|---|---|---|
| **Numerical Ability** | 20-26 | 25-40 min | Arithmetic-heavy; see `02_Aptitude_and_Quant/` |
| **Verbal Ability** | 22-25 | 25 min | Grammar, RC, sentence completion |
| **Reasoning Ability** | 20-30 | 25-40 min | Series, puzzles, data sufficiency, visual reasoning |
| **Programming Logic / MCQ** | 10-20 | 15-20 min | Pseudocode output, C/C++/Java/Python concepts, DS basics |
| **Coding (Hands-on)** | 1-2 | 15-30 min | Console programs with `stdin`/`stdout` |
| **Advanced Quantitative** (Prime) | ~15 | ~25 min | Harder arithmetic and modern maths |
| **Advanced Coding** (Prime) | 1-2 | ~45-90 min | Genuine DSA problems |

**Platform:** TCS's own **iON** platform (also used for TCS CodeVita and many PSU exams).
**Proctoring:** webcam, screen-share, AI proctoring, ID verification. It is strict.

---

## 3. Critical iON platform mechanics ⚠️⭐⭐⭐

These cost more marks than weak preparation does:

1. **Sectional timing and no going back.** Most NQT sittings lock each section once its timer
   expires. **You cannot return.** Budget per question and move on ruthlessly.
2. **No calculator on screen** in most variants — only a basic one, sometimes none. Mental
   arithmetic speed is a real, scored skill here. Learn the tables to 25, squares to 30, cubes to
   15, and the fraction-to-percentage table in `02_Aptitude_and_Quant/Percentages.md`.
3. **Negative marking** appears in some sittings (typically only for a subset of questions, or for
   the Advanced sections). Read the instruction page — it is displayed before the test and people
   skip it.
4. **The coding compiler is basic.** No autocomplete, plain editor, sometimes a slightly old
   compiler version. Practise on iON's own mock if the placement cell provides it.
5. **Input format is strict.** Read exactly as specified; extra prompt text printed to stdout
   ("Enter a number:") can fail the test case. ⚠️ **Print only what is asked.**

> **The single most common NQT coding failure is printing a prompt string.** Your program must
> read silently and print only the answer.

---

## 4. Section-by-section strategy

### Numerical Ability
Highest-yield topics, in order:

| Topic | Why | Where |
|---|---|---|
| Percentages, profit & loss | Appears every time, multiple questions | `Percentages.md`, `Profit_and_Loss.md` |
| Time, speed, distance (incl. trains, boats) | 2-4 questions | `Time_Speed_Distance.md` |
| Time and work, pipes and cisterns | 2-3 questions | `Time_and_Work.md` |
| Ratio, proportion, mixtures, ages | 2-4 questions | `Ratio_Proportion_and_Mixtures.md` |
| Number system, LCM/HCF, divisibility, remainders | 2-4 questions | `Number_System.md` |
| Simple and compound interest | 1-2 questions | `Simple_and_Compound_Interest.md` |
| Permutation, combination, probability | 1-3 questions | `Probability_and_Combinatorics.md` |
| Averages | 1-2 questions | `Averages_and_Ages.md` |
| Mensuration, area/volume | 1-2 questions | `Geometry_and_Mensuration.md` |
| Data interpretation (tables, pie, bar) | 1 set = 3-5 questions ⭐ | `Data_Interpretation.md` |

⭐ **DI sets are the best marks-per-minute in the paper** if your arithmetic is fast: one set of
context, three to five questions.

### Verbal Ability
- Sentence correction / error spotting (subject-verb agreement, tense, prepositions, articles)
- Sentence completion and synonym/antonym in context
- Para-jumbles (find the opening sentence first, then the pronoun chains)
- Reading comprehension: 1-2 passages, 3-5 questions each
See `04_Verbal_Ability/`.

### Reasoning Ability
- Number and letter series, odd one out
- Coding-decoding, blood relations, direction sense
- Seating arrangement and puzzles (linear, circular, floor/box)
- Syllogisms, statement-conclusion, data sufficiency
- Visual/figure series, mirror and water images, paper folding ⭐ (NQT-specific — many other
  companies dropped these but TCS still uses them)
See `03_Logical_Reasoning_and_Puzzles/`.

### Programming Logic (MCQ)
This is a **pseudocode and fundamentals** section, not a coding section. Expect:
- Predict the output of a pseudocode block (loops, recursion, arrays)
- Time complexity of a given snippet
- Data structure properties: stack vs queue, array vs linked list, tree traversal orders
- Complexity of standard algorithms (sorting, searching, hashing)
- Basic C concepts: pointers, storage classes, operator precedence, `#define` vs `const`
- OOP: encapsulation, polymorphism, inheritance types
See `05_MCQ_Core_CS_Banks/`.

### Coding (Hands-on)
Difficulty: **easy**. This is deliberate — TCS is checking that you can write and run a program,
not that you know algorithms.

Typical problems:
```
- sum/average of an array; largest/smallest; second largest
- string reverse, palindrome, vowel count, word count
- factorial, Fibonacci, prime check, prime in range
- Armstrong / perfect / Nirantar numbers, digit sum, digit reversal
- simple pattern printing (pyramid, diamond)
- swap without a third variable
- matrix addition, transpose
- GCD/LCM
- simple menu-driven arithmetic with conditional rules
- a small "business rule" word problem: compute a bill, a discount, a grade
```

**Advanced Coding** (Prime band) is a real DSA problem: sorting + greedy, hash map, two pointers,
simple DP, or a graph BFS. Prepare it like an Amazon OA problem but one notch easier.

---

## 5. A worked example of the NQT coding style

*"Read N, then N integers. Print the sum of all even numbers and the sum of all odd numbers,
separated by a space."*

```c
#include <stdio.h>
int main(void) {
    int n;
    if (scanf("%d", &n) != 1) return 0;   /* read silently: NO prompt text */
    long long even = 0, odd = 0;
    for (int i = 0; i < n; i++) {
        int x;
        scanf("%d", &x);
        if (x % 2 == 0) even += x; else odd += x;
    }
    printf("%lld %lld\n", even, odd);      /* print exactly what is asked */
    return 0;
}
```

⚠️ Note the three things that earn or lose the mark: no prompt string, `long long` for the sum,
and the exact output separator.

---

## 6. Time budget discipline

With ~25 minutes for ~25 numerical questions you have **60 seconds per question** including
reading. That means:

```
 0-20 s : read and classify.  Do I have a formula/shortcut for this?
          NO  → mark and move on immediately.  ⭐ this is the skill
 20-50 s: solve
 50-60 s: mark and go
```

> **The NQT rewards abandonment.** Attempting 18 questions with 17 correct beats attempting 25
> with 15 correct if there is negative marking, and beats it on time even if there is not.

---

## 7. Preparation plan (6 weeks)

| Week | Numerical | Verbal | Reasoning | Programming |
|---|---|---|---|---|
| 1 | Number system, percentages | Grammar rules | Series, coding-decoding | C basics, pseudocode |
| 2 | Profit-loss, ratio, average | RC technique | Blood relations, directions | Arrays, strings programs |
| 3 | TSD, time-work | Para-jumbles, vocabulary | Seating arrangements | Patterns, recursion |
| 4 | SI/CI, P&C, probability | Timed RC sets | Syllogism, data sufficiency | Complexity MCQs, DS basics |
| 5 | Mensuration, DI | Full verbal mocks | Figure/visual reasoning | Advanced coding problems |
| 6 | **Full mocks only**, 3-4 of them, strictly timed, logged in `06_Timed_Mock_Logs/` | | | |

---

## 8. Test-day checklist

- [ ] Read the instruction page — note negative marking and whether sections lock
- [ ] Keep rough sheets and a pen ready (allowed; keep the desk otherwise clear for the proctor)
- [ ] Attempt the easy 60% of every section first, then return within the section's own timer
- [ ] In coding: read input exactly, print output exactly, no prompt strings
- [ ] Attempt the Advanced sections even if unsure — they gate the higher band
- [ ] Do not switch tabs, do not look away for long, keep your face in frame
