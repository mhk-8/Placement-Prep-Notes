
# The Technical Interview Protocol

> **What this file is about.** Not *what* to know — that is `../../01_DSA/` and `../../02_Core_CS/`.
> This is about **how to behave in the room**, which is scored separately and which most candidates
> never practise.

---

## 1. What is actually being graded ⭐⭐⭐

A DSA round is not a test of whether you can produce the optimal solution. It is a simulation of
working with you on a hard problem. Most companies score on four axes:

| Axis | What it means | How you lose it |
|---|---|---|
| **Problem solving** | Do you find a working approach, and improve it? | Jumping to code; getting stuck silently |
| **Coding** | Is the code correct, clean, and does it compile? | No edge cases; unreadable naming; bugs you do not catch |
| **Communication** | Can they follow your thinking? | Long silences; explaining only after you finish |
| **Verification** | Do you test your own work? | Saying "done" without dry-running |

⭐ **The implication:** a candidate who reaches a clean `O(n log n)` while narrating clearly and
testing carefully outscores a candidate who silently produces the optimal `O(n)` and never checks
it. Both "solved it"; only one demonstrated how they work.

---

## 2. The six-step protocol ⭐⭐⭐

Follow this every time, in this order, out loud.

```
1. CLARIFY        (1-2 min)  Restate the problem. Ask about input size, types, duplicates,
                             negatives, empty input, sorted-ness, and what to return on failure.
2. EXAMPLES       (2-3 min)  Work the given example BY HAND. Then construct your own edge cases.
3. BRUTE FORCE    (2 min)    State an approach that definitely works, and its complexity.
                             ⭐ You now have a fallback. The pressure drops immediately.
4. OPTIMISE       (5 min)    Find the bottleneck in the brute force. Name the pattern that
                             removes it. Get the interviewer's agreement BEFORE coding.
5. CODE           (10-15 min) Write it, narrating the non-obvious lines.
6. TEST           (3-5 min)  Dry-run on a small example, line by line, with a variable table.
                             Then state the final time and space complexity.
```

⚠️ **Steps 1-4 take about half the interview and candidates routinely skip them.** Writing code
before the interviewer has agreed to your approach is the most expensive mistake available: if the
approach is wrong you have burned fifteen minutes, and if it is right you have lost the chance to
show you can reason.

---

## 3. Step 1 — clarifying questions worth asking ⭐⭐

A short, universal list. Ask three or four, not all of them.

```
□ "What's the expected input size?"          → tells you the target complexity (see §4)
□ "Can the input be empty, or a single element?"
□ "Can values be negative? Zero?"
□ "Can there be duplicates?"
□ "Is the input sorted, or can I sort it?"   ⭐ often the whole problem
□ "Are there memory constraints, or can I use O(n) extra space?"
□ "What should I return if there's no valid answer?"
□ "Should I modify the input in place, or is a copy fine?"
□ For strings: "ASCII or Unicode? Case-sensitive?"
□ For graphs: "Directed? Weighted? Can there be cycles? Self-loops?"
```

⭐ **Ask them because you need the answer, not to perform.** An interviewer can tell the difference
between a candidate genuinely scoping a problem and one reciting a checklist. If the input size is
already given, do not ask for it.

---

## 4. The constraints → complexity table ⭐⭐⭐

Read the constraint **first**. It usually tells you which family of solutions is intended, before
you have thought about the problem at all.

| Constraint | Intended complexity | Typical technique |
|---|---|---|
| `n ≤ 10` | `O(n!)` / `O(2ⁿ · n)` | Permutations, full search |
| `n ≤ 20-25` | `O(2ⁿ)` | Bitmask DP, meet in the middle |
| `n ≤ 100-500` | `O(n³)` | Floyd-Warshall, interval DP |
| `n ≤ 5,000` | `O(n²)` | 2-D DP, all-pairs on small input |
| `n ≤ 10⁵-10⁶` | `O(n log n)` | Sort, heap, binary search, segment tree |
| `n ≤ 10⁷-10⁸` | `O(n)` | Two pointers, prefix sums, sieve, counting |
| `n ≤ 10¹⁸` | `O(log n)` | Binary search on the answer, maths, matrix exponentiation |

---

## 5. Thinking aloud — what it actually sounds like ⭐⭐⭐

"Think aloud" is universal advice and almost nobody practises it. Concretely, narrate **decisions
and their reasons**, not keystrokes.

```
❌ SILENT      : [45 seconds of nothing] "Okay, I'll use a hash map."
❌ NARRATING KEYSTROKES: "Now I'm writing a for loop. i equals zero..."
✅ NARRATING REASONING :
     "The brute force is checking every pair, which is O(n²). The repeated work is the
      lookup — for each element I'm re-scanning to find its complement. That's the kind of
      lookup a hash map makes constant, so I think a single pass with a hash map gets this
      to O(n) time and O(n) space. Does that sound reasonable before I code it?"
```

**The three moments you must narrate:**
1. When you choose an approach — say *why*, and name the alternative you rejected.
2. When you get stuck — say *where* (see §6).
3. When you write a non-obvious line — one short sentence, not a lecture.

---

## 6. When you are stuck ⭐⭐⭐

Being stuck is expected. **Being silently stuck is the failure.** The recovery script:

```
"Let me say where I am. I can see that <the part I understand>. What I'm stuck on is
 <the specific blocker>. My instinct is <hypothesis>, but I'm not sure because <reason>.
 Let me try a small example and see what happens."
```

Then actually try the small example, out loud.

**Why this works:** it converts "this candidate is stuck" into "this candidate has isolated the
difficulty", which is what a colleague does. It also lets the interviewer give you a targeted hint
instead of watching you flounder — and giving useful hints is *their* job.

**Unsticking techniques, in the order to try them ⭐:**
```
1. Do a smaller case by hand (n = 1, 2, 3) and look for the pattern
2. Ask what the brute force repeats — repeated work is where every optimisation lives
3. Ask "what would I need to know at step i to answer in O(1)?" → suggests the state to cache
4. Try the standard patterns against it: sort? hash map? two pointers? heap? binary search
   on the answer? DP? graph?
5. Relax a constraint, solve the easier problem, then reintroduce the constraint
6. Work backwards from the required output
```

---

## 7. Taking hints well ⭐⭐

Interviewers give hints deliberately. How you receive one is scored.

```
❌ "Oh right, obviously." → dismissive, and it sounds like you are covering
❌ Ignoring it and continuing on your own path → the worst outcome; they will stop helping
✅ "That's useful — so if I sort first, the two-pointer scan becomes valid because
    <reason>. Let me redo the complexity: sorting is O(n log n), the scan is O(n),
    so overall O(n log n)."
```

⭐ **Engage with the hint explicitly and extend it.** That shows you can be collaborated with,
which is precisely what the round is simulating. Needing zero hints is not the target; needing a
hint and using it well scores better than silently failing.

---

## 8. Step 5 — code hygiene in a shared editor ⭐⭐

There is no autocomplete, no compiler and no syntax highlighting in many interview editors. Adjust.

```
□ Meaningful names. `left`, `right`, `seen`, `freq` — not `i`, `j`, `a`, `temp`.
□ Blank lines between logical blocks. Readability is scored.
□ Handle the edge case explicitly at the top:  if (!nums || nums.empty()) return ...;
□ Write helper functions for anything non-trivial; do not nest four levels deep.
□ Do NOT abbreviate to save typing. Nobody is timing your keystrokes.
□ If you use a language feature that might be unfamiliar, say what it does in four words.
□ State the complexity in a comment at the end. It costs nothing and graders look for it.
```

⚠️ **Language choice:** use the one you are fastest in, not the one that looks impressive. For you
that is C++ or Python. If you pick C++, do not get trapped in memory management you do not need —
use `vector`, `unordered_map` and range-for.

---

## 9. Step 6 — dry-running properly ⭐⭐⭐

"Testing" does not mean re-reading your code. It means **executing it by hand with a variable
table**.

```
Input: nums = [2, 7, 11, 15], target = 9

i | nums[i] | complement | seen           | action
--|---------|------------|----------------|------------------
0 |    2    |     7      | {}             | not found → seen[2] = 0
1 |    7    |     2      | {2:0}          | FOUND → return [0, 1]   ✓
```

Then state the cases you have covered:
```
"I've checked the normal case. Let me think about edge cases: empty input returns
 [] at the guard; a single element can't form a pair, same guard; duplicates —
 [3,3] with target 6 works because I insert after checking, not before.  ⭐
 Negative numbers are fine since I never assume positivity."
```

⭐ **Finding your own bug during the dry run is a positive signal, not a negative one.** It is
strictly better than the interviewer finding it. Never rush this step to "finish early" — there is
no prize for finishing early.

---

## 10. Common failure modes ⚠️

| Failure | Fix |
|---|---|
| Coding before agreeing on the approach | Always get a nod at step 4 |
| Silence while thinking | Narrate, even if it is "let me think about the invariant here" |
| Over-explaining when asked a short question | Answer, then stop. Let them steer |
| Defending a wrong answer when corrected | Check it, then concede cleanly and move on ⭐ |
| Claiming a complexity you have not verified | Count the loops out loud |
| Giving up on the optimal and not writing the brute force | **Always** have working code |
| Not asking about input size, then choosing the wrong approach | Step 1 |
| Running out of time on a perfect solution | Time-box the optimisation; code at the 20-minute mark |

---

## 11. The last five minutes ⭐

```
□ Confirm the code compiles mentally — no missing return, no unclosed brace
□ State final time and space complexity
□ Volunteer one limitation or one improvement ("this assumes the input fits in memory;
  for a stream I'd use a different approach")
□ Ask your questions (see ../05_Questions_To_Ask/)
```

---

## 12. The one practice exercise that matters ⭐⭐⭐

```
Record yourself solving ONE medium problem aloud, from clarification to dry run,
in a plain text editor with no autocomplete. Then watch it back.
```

It is deeply uncomfortable and it is the fastest improvement available. You will discover: long
silences you did not notice, filler words, skipping straight to code, and never stating complexity.
Do it once a week. One recorded-and-reviewed problem beats five silently solved ones.

---

## Recall questions

1. Name the four scoring axes and how you lose each one.
2. Recite the six steps, with their rough time budgets.
3. Give five clarifying questions worth asking for a general array problem.
4. What complexity is intended when `n ≤ 20`? When `n ≤ 10⁶`?
5. Give the exact script for being stuck.
6. How do you receive a hint well?
7. What does a proper dry run look like?
8. What is the single highest-value practice exercise, and why is it avoided?
