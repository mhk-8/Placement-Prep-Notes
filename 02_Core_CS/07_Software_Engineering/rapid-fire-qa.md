# Software Engineering — Rapid Fire Q&A

> These come up in *every* interview, attached to your project story. Generic answers are worth almost nothing here; specificity is the entire signal.

**1. Walk me through your git workflow.** Branch off main for each change, commit in small logical units, rebase onto main before pushing, and open a pull request for review. *(Then name the strategy your team used and one problem it caused.)*

**2. Merge or rebase?** Rebase locally to keep history linear before pushing; merge for shared branches. The rule is never to rebase anything others have already pulled, because rewritten hashes force everyone into recovery.

**3. How do you undo a pushed commit?** `git revert`, which adds a new commit undoing the change. `reset` rewrites history and is only safe while the branch is private.

**4. How do you find which commit introduced a bug?** `git bisect` with a known-good and known-bad commit — binary search over history, so a thousand commits take about ten tests. `git bisect run` automates it with a test script.

**5. How do you write a commit message?** A short imperative subject, then a body explaining *why* rather than what. The diff already shows what changed; the message must explain the reasoning a future reader will not have.

**6. Describe your testing approach.** Many fast unit tests, fewer integration tests, and a small number of end-to-end tests over critical flows. Every bug fix ships with a test that fails before the fix and passes after.

**7. Stub, mock or fake?** A stub returns canned data, a mock also asserts how it was called, a fake is a working lightweight implementation. I prefer fakes and stubs, because heavy mocking couples tests to implementation and they break on every refactor.

**8. How much coverage is enough?** Coverage tells you what *ran*, not what was *checked*, so I use it as a floor rather than a target. A sharp drop signals untested new code; chasing the number produces assertion-free tests.

**9. What makes a test good?** Deterministic, isolated, fast, and written against behaviour rather than implementation. If renaming a private method breaks a test, the test was testing the wrong thing.

**10. Have you used TDD?** *(Answer honestly.)* Its real value is design pressure — code that is painful to test is usually badly coupled — so I reach for it most when the design is unclear rather than as a universal rule.

**11. How do you debug a problem you cannot reproduce?** Start by making it reproducible: narrow the inputs, check logs for the failing requests, and add structured logging at the boundaries with correlation IDs. An unreproducible bug cannot be verified as fixed, so that step is not optional.

**12. Walk me through debugging a real bug you hit.** *(Have one ready: the symptom, your hypothesis, how you tested it, the actual cause, and the regression test you added. This is one of the most revealing questions asked, and a rehearsed concrete story is worth far more than a methodology.)*

**13. Logging or breakpoints?** Breakpoints for a local reproducible bug; structured logging for production, concurrency and distributed systems, where stopping the world is not an option. Log at boundaries rather than everywhere, or the signal drowns.

**14. What do you look for in a code review?** Correctness and edge cases first, then security, then readability and naming. Style should be handled by a formatter, so review time is not spent on it.

**15. How do you give review feedback?** Specific and about the code, not the person — "this loop queries per row, a join would avoid the N+1" rather than "this is inefficient". I also try to distinguish blocking issues from suggestions explicitly.

**16. What is technical debt, and is it always bad?** A deliberate shortcut taken to ship sooner, paid back later in slower changes. It is a legitimate trade when it is conscious and tracked; it becomes a problem when it is invisible and unbudgeted.

**17. What does DRY actually mean?** One authoritative source per piece of *knowledge* — not a ban on similar-looking code. Two functions that happen to look alike but change for different reasons should stay separate, because merging them couples unrelated things.

**18. Describe the agile process you worked in.** *(Describe what actually happened, including what did not work. "Standups drifted into status reports" is a better answer than reciting the Scrum guide.)*

**19. CI vs CD?** Continuous integration builds and tests on every push. Continuous delivery keeps every passing build releasable; continuous deployment actually releases it automatically.

**20. What was in your CI pipeline?** *(Be specific: lint, unit tests, integration tests, build, deploy to staging. If you had none, say so and say what you would add first — that answer is fine and honest.)*

**21. Blue-green, canary or rolling?** Canary for the best risk control, since you see real errors on a small traffic slice first. Blue-green for the fastest rollback. Rolling when infrastructure cost matters most.

**22. What are feature flags for?** Decoupling deployment from release — code ships dark and is enabled separately, which makes trunk-based development workable and rollback instant. The cost is flag cleanup, which teams routinely forget.

**23. Why Docker?** The same image runs identically on a laptop, CI and production, which eliminates environment drift. A container shares the host kernel, so it starts in milliseconds rather than the seconds a VM needs.

**24. How would you onboard someone onto your project?** A README that gets them running in one command, an architecture overview of how the pieces fit, and a small first task with a reviewer attached. The measure is how long until their first merged pull request.

**25. What would you do differently on your last project?** *(Have a real answer. Something like "we deferred tests until the end and paid for it during the final integration" shows reflection. Refusing to name anything reads as either inexperience or defensiveness.)*
