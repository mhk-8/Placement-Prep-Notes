# Software Engineering — Flashcards

## Questions

1. Name git's three areas and the commands that move changes between them.
2. Merge vs rebase, and the golden rule about shared branches.
3. Compare `reset --soft`, `--mixed` and `--hard`.
4. When do you use `revert` rather than `reset`?
5. What does `git bisect` do, and how many steps for 1000 commits?
6. What is `git reflog` for?
7. Describe the testing pyramid and its common inversion.
8. Stub vs mock vs fake?
9. What does code coverage actually measure, and why is it a bad target?
10. What are the three Scrum roles and four ceremonies?
11. Distinguish continuous integration, delivery and deployment.
12. Compare blue-green, canary and rolling deployments.
13. What are feature flags for, and what do they cost?
14. Why is a container lighter than a VM?
15. Why copy `requirements.txt` before the source in a Dockerfile?
16. What does DRY actually mean, and when does deduplication hurt?
17. Give a seven-step debugging methodology.
18. When do you prefer logging over breakpoints?
19. What should a code review focus on, and what should it not?
20. What is technical debt, and when is it acceptable?

---

## Answers

1. Working directory → staging area (index) → repository. `git add` moves to the index; `git commit` moves to the repository.
2. Merge creates a merge commit and preserves the real graph; rebase replays commits for a linear history but rewrites hashes. Never rebase a branch others have already pulled.
3. `--soft` moves HEAD only (changes stay staged); `--mixed` also resets the index (changes stay in the working tree); `--hard` also discards the working tree.
4. Whenever the commit is already pushed. `revert` adds a new undoing commit rather than rewriting shared history.
5. Binary-searches history between a known-good and known-bad commit to find where a bug was introduced — about **10** tests for 1000 commits, and fully automatable with `git bisect run`.
6. It records every position HEAD has held, so a bad `reset` or deleted branch can be recovered for roughly 90 days.
7. Many fast unit tests, fewer integration tests, very few end-to-end tests. The inversion — mostly slow e2e tests — makes the suite so slow that people stop running it.
8. Stub returns canned answers; mock additionally asserts *how* it was called; fake is a working lightweight implementation such as an in-memory repository.
9. Which lines executed — not whether anything was verified. A suite with no assertions can reach 100%. It is a useful floor and a harmful target.
10. Roles: product owner, scrum master, development team. Ceremonies: sprint planning, daily standup, sprint review, retrospective.
11. CI builds and tests every push; continuous delivery keeps every passing build releasable; continuous deployment releases it automatically.
12. Blue-green: two full environments, instant switch and instant rollback. Canary: a small traffic percentage first, best risk control. Rolling: replace instances gradually, cheapest in infrastructure.
13. They decouple deployment from release, so code ships disabled and is turned on separately — enabling trunk-based development and instant rollback. The cost is flag cleanup, which teams routinely neglect.
14. It shares the host kernel and isolates only processes via namespaces and cgroups, instead of booting a full guest OS on emulated hardware.
15. Docker caches layers and invalidates everything after the first change, so installing dependencies before copying the source lets an ordinary code change reuse the cached install layer.
16. One authoritative source per piece of *knowledge*. Deduplicating code that merely looks similar but changes for different reasons couples unrelated things and makes both harder to change.
17. Reproduce reliably; isolate; form a hypothesis; test one change at a time; fix the cause not the symptom; add a regression test; ask why it was not caught.
18. For production, concurrency and distributed systems, where you cannot stop the world. Log at boundaries with correlation IDs rather than everywhere.
19. Correctness and edge cases, then security, then readability and naming. Not style — a formatter should handle that automatically.
20. A deliberate shortcut taken to ship sooner, repaid later in slower changes. Acceptable when it is conscious, tracked and budgeted; harmful when it is invisible.
