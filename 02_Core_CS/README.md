# 02 — Core Computer Science

Core CS carries the MCQ sections of most OAs and 20–40% of technical interview time. It is also the **cheapest section to score in**, because the question bank is finite and stable — unlike DSA, where the problem space is unbounded.
**Last filled: 2026-09-17.**

## Files in every subfolder

| File | What it is | When you read it |
|---|---|---|
| `concepts.md` | The theory, with recall questions at the end | First pass, then at gates |
| `numericals.md` | Worked numerical problems with full solutions | Every OA-prep session — this is where marks are won |
| `oa-solved-questions.md` | **Solved OA/MCQ bank** with the reasoning for every option | Daily drill, timed |
| `rapid-fire-qa.md` | 30–40 short interview answers in your own words | Before every interview |
| `diagrams.md` | The diagrams you must be able to draw from memory | Weekly, on paper |
| `flashcards.md` | Questions first, answers below the rule | D+1/D+3/D+7/D+21 |

*(OOP has `output-prediction.md` in place of `numericals.md`; Software Engineering has neither.)*

## Subfolders and their weight

| Folder | OA weight | Interview weight | Priority |
|---|---|---|---|
| `01_Operating_Systems` | High — numericals | High | **P1** |
| `02_DBMS_and_SQL` | **Highest** — SQL section + MCQs | High | **P1** |
| `03_Computer_Networks` | Medium — subnetting, protocols | Medium | P2 |
| `04_OOP` | High — output prediction | High | **P1** |
| `05_Computer_Architecture` | Medium — pipeline, cache | Low | P2/P3 |
| `06_Compilers_and_TOC` | Low–medium (GATE-style OAs) | Low | P3 |
| `07_Software_Engineering` | Low | Medium (project talk) | P2 |

## How core CS is actually tested

**In OAs:** 20–30 rapid MCQs, often with negative marking, in 20–25 minutes. That is under a minute per question, so recall must be automatic — you are not deriving, you are recognising. Roughly half are numericals (scheduling tables, page faults, subnetting, pipeline speedup) and half are conceptual or output-prediction.

**In interviews:** depth over breadth. Expect three to four follow-ups on whatever you claim to know, and expect to be asked to connect theory to your own projects. "We used a thread pool" invites every question in `01_Operating_Systems`.

## Negative marking changes your strategy

With −0.25 for a wrong answer, skip anything you are under roughly 60% confident on. With no negative marking, never leave a blank. **Check which regime you are in before the section starts** — it is stated on the instruction page that everyone skips.

## Study order

1. **OS + DBMS first.** They carry the most weight in both OAs and interviews.
2. **OOP alongside them** — it is short and it feeds the LLD round.
3. **CN next**, focusing on subnetting arithmetic and the "type a URL" narrative.
4. **Architecture and TOC last**, and only if time remains after the P1 rows in `00_Start_Here/syllabus-checklist.md` are at 4+.

## The rule that matters here

Core CS rewards **written recall, not reading**. Every one of these subfolders ends with flashcards and a diagram set for exactly that reason. Close the notes, write the answer on paper, then check. Re-reading a note feels like learning and is not.
