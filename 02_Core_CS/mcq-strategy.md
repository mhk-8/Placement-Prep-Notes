# Core CS MCQ Strategy

> How to convert knowledge into marks in a 25-minute, 30-question section. Read this before every mock.

---

## 1. Before the section starts

- [ ] **Read the instruction page.** Negative marking? Can you revisit questions? Is the section separately timed?
- [ ] Note the marks-per-question — some OAs weight numericals double.
- [ ] Decide your skip threshold from the marking scheme (below).

## 2. The marking-scheme decision

| Scheme | Strategy |
|---|---|
| No negative marking | **Answer everything.** A blank is strictly worse than a guess. |
| −0.25 per wrong | Break-even at 25% confidence. With 4 options, eliminating even one makes guessing +EV. Skip only if you cannot eliminate any option. |
| −0.33 per wrong | Break-even at 25%. Same as above, slightly tighter. |
| −1 per wrong (rare) | Answer only when confident or when two options are eliminated. |

**The arithmetic:** with 4 options and a −0.25 penalty, a blind guess has expected value 0.25(1) + 0.75(−0.25) = **+0.0625**. Eliminating one option raises it to +0.17. Eliminating two gives +0.375. So under the common scheme, **guessing after eliminating one option is always correct**.

## 3. Time allocation

30 questions in 25 minutes = 50 seconds each. That is not enough for the numericals, so:

| Pass | Time | What |
|---|---|---|
| 1 | 12 min | Answer everything you know in under 30 seconds. Mark the rest. |
| 2 | 9 min | The numericals — scheduling tables, page faults, subnetting. These take 90–120 s each, so budget 5–6 of them. |
| 3 | 4 min | Marked questions: eliminate and guess per the scheme above. |

**Never do the numericals first.** They consume the clock while easy conceptual marks sit unanswered.

## 4. Elimination techniques

- **Units and magnitude.** A page-fault answer of 1.4 when 10 references were made is impossible. An effective access time below the cache hit time is impossible.
- **Extreme options.** "Always" and "never" are usually wrong in conceptual questions; "may" and "can" are usually right.
- **Two options that mean the same thing** are both wrong — the answer is one of the other two.
- **The odd one out.** If three options share a form and one differs, the different one is often either the answer or an obvious distractor. Look at what the three agree on.
- **Longest, most qualified option** tends to be correct in conceptual MCQs, because correctness needs qualifications.
- **Back-substitute.** For numericals, plugging an option into the constraint is often faster than solving forwards.

## 5. The traps question-setters use

| Trap | Example |
|---|---|
| Swapping two similar terms | mutex vs semaphore · preemptive vs non-preemptive · clustered vs non-clustered index |
| Off-by-one in a definition | "Belady's anomaly occurs in LRU" (it is FIFO) |
| Correct fact, wrong context | "TCP guarantees delivery" (it guarantees *reliable delivery or failure notification*, not delivery) |
| Right answer to a different question | asking for *turnaround* time and listing the *waiting* times |
| Best case quoted as worst case | "Quicksort is O(n log n)" (worst is O(n²)) |
| Average quoted as guaranteed | "Hash lookup is O(1)" (average, not worst) |
| Stability claims | "std::sort is stable" (it is not) |
| Unit switches | ms vs µs · bits vs bytes · KB vs KiB |

## 6. Per-subject speed rules

**OS numericals.** Draw the Gantt chart. Always. Trying to do a scheduling question mentally is how you lose 90 seconds and still get it wrong. Write the completion times under the chart, then compute TAT = CT − AT and WT = TAT − BT.

**Paging.** Write the EAT formula before plugging numbers: `EAT = h(TLB + mem) + (1−h)(TLB + 2·mem)` for the standard single-level case. Getting the formula out of your head first prevents the classic "forgot the second memory access" error.

**Subnetting.** Convert the mask to "block size = 256 − last non-zero octet", then count. Never convert to binary under time pressure.

**SQL.** Read the expected output shape first (one row? one row per group?), then work backwards to whether you need GROUP BY, a window function, or a self-join.

**OOP output prediction.** Trace on paper with a two-column table: variable → value. Mentally tracing inheritance chains is unreliable.

## 7. After the section

Log every miss in `10_Mistake_Log_and_Revision/mistake-log.md` with its root-cause tag. For MCQs the tags are usually `concept` (did not know it) or `misread` (knew it, answered a different question). Those need different fixes, and the split tells you which.
