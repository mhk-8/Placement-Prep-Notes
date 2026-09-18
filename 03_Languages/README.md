# 03 — Languages & Libraries

Fluency is a **time multiplier**. In a 90-minute OA, fumbling with syntax or hunting for the right container costs you a whole problem — and that is the difference between clearing a cutoff and not.
**Last filled: 2026-09-18.**

## Files in every language folder

| File | What it is | When you read it |
|---|---|---|
| `syntax-reference.md` | The syntax you must type without thinking | First pass, then skim weekly |
| `library-complexities.md` | Every container and algorithm with its cost | Before every OA — this is the lookup table |
| `snippets.md` | Copy-ready blocks: fast I/O, comparators, common idioms | Keep open while practising |
| `gotchas.md` | The traps that silently produce wrong answers | Before every submission |
| `oa-solved-questions.md` | Solved output-prediction and MCQ questions, every option explained | Daily drill |
| `flashcards.md` | Questions first, answers below the rule | D+1 / D+3 / D+7 / D+21 |

*(SQL has `query-patterns.md` in place of `library-complexities.md`, since the cost model there is the query planner's, not a container's.)*

## The folders

| Folder | Role | Priority |
|---|---|---|
| `CPP` | The OA workhorse — fastest execution, richest competitive library | **P1** if it is your primary |
| `Python` | Fastest to write, mandatory for ML roles, risky on tight time limits | **P1** if it is your primary, P2 otherwise |
| `Java` | Standard at many MNCs; strong typing catches what Python hides | P1 if primary, P2 otherwise |
| `SQL` | A **separate OA section** at many data-oriented companies | **P1 for everyone** |

## Pick one primary language and commit

This decision belongs in Week 0 and must be frozen by **18 Oct** (`00_Start_Here/rules.md`, rule 9). Changing your OA language in November is how people lose offers. See `language-choice.md` for the trade-offs.

**You still need a working reading knowledge of the other two**, because output-prediction MCQs appear in whichever language the setter chose, not the one you write in.

## What "fluent" means, concretely

- [ ] You can type your fast-I/O preamble from memory in under 20 seconds
- [ ] You know, without looking, which container gives O(log n) ordered lookup and which gives O(1) unordered
- [ ] You can write a custom comparator for a struct, a lambda and a pair — three different ways
- [ ] You can write 20 lines and have them compile on the first attempt
- [ ] You know your language's overflow, recursion-depth and division-rounding behaviour cold

Anything unticked after Gate A is a study session, not a "I'll pick it up as I go".

## How this folder relates to the others

`01_DSA/templates.md` holds the **algorithm** templates in Python with C++ notes. This folder holds the **language** — the containers, their costs, the I/O, and the traps. When an algorithm template and a language reference disagree, the language reference is authoritative on syntax and the algorithm template on structure.

SQL is the exception to the split: `02_Core_CS/02_DBMS_and_SQL` covers the *theory* (normalisation, transactions, indexes) and the query-writing problems; `SQL/` here covers the *language* — functions, dialect differences, and the patterns you type.
