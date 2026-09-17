# 10 — Mistake Log & Revision

The highest-ROI folder in this vault. Most people study; few systematically eliminate their own recurring errors.

## Files
- `mistake-log.md` — every wrong answer, same day
- `recurring-patterns.md` — mistakes that appear 3+ times, promoted here with a fix
- `spaced-repetition-queue.md` — what is due today
- `concept-gaps.md` — things you keep half-knowing
- `careless-bugs.md` — off-by-one, uninitialised, wrong variable, integer overflow, wrong comparator

## Mistake log entry format

```
### 2026-09-17 — <problem or question>
Source: LeetCode 239 / Amazon OA mock / DBMS MCQ set 3
What I did: ...
Why it was wrong: ...
Correct approach: ...
Root cause: [concept gap | careless bug | time pressure | misread the question]
Fix / rule for next time: ...
Revisit: D+3 (2026-09-20), D+7, D+21
```

## The weekly ritual (Sunday, 60 minutes)
1. Reread every entry added this week.
2. Re-solve, from scratch, three problems you got wrong.
3. Promote anything appearing 3+ times into `recurring-patterns.md` with an explicit rule.
4. Update `00_Start_Here/Trackers/topic-revision-tracker.md`.

Root-cause tagging matters: a concept gap needs study, a careless bug needs a checklist, time pressure needs more timed mocks. They are not the same problem and they do not have the same fix.
