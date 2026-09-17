# Software Engineering — Solved OA Questions

> 12 questions. SE carries low OA weight — expect a handful of git and agile MCQs at most — so this set is deliberately short. The real value of this folder is the interview conversation, which lives in `rapid-fire-qa.md`.

---

**Q1.** Which git command undoes a commit that has **already been pushed** to a shared branch?
(a) `git reset --hard HEAD~1`  (b) `git revert <commit>`  (c) `git commit --amend`  (d) `git checkout HEAD~1`

<details><summary>Answer</summary>

**(b) `git revert`.**

It creates a **new** commit that undoes the change, leaving history intact. (a) and (c) rewrite history, so anyone who already pulled is forced into a recovery; (d) just moves HEAD into a detached state and undoes nothing.

**The rule:** rewrite history only while it is private. Once it is pushed, use `revert`.
</details>

---

**Q2.** `git merge` differs from `git rebase` in that
(a) merge rewrites history, rebase does not
(b) rebase replays commits to produce a linear history and rewrites hashes
(c) they are identical
(d) merge cannot handle conflicts

<details><summary>Answer</summary>

**(b).**

Merge creates a merge commit and preserves the true graph; rebase replays your commits onto a new base, producing a linear history at the cost of new commit hashes.

(a) is exactly backwards, and is the intended trap. Both can conflict, so (d) is false.
</details>

---

**Q3.** `git reset --soft HEAD~1` does what?
(a) Deletes the last commit and all its changes
(b) Moves HEAD back one commit, keeping the changes **staged**
(c) Creates a new commit
(d) Switches branch

<details><summary>Answer</summary>

**(b).**

`--soft` moves HEAD only. `--mixed` (the default) also resets the index, leaving changes in the working tree unstaged. `--hard` additionally discards the working tree — the one destructive variant.

Remember them as an increasing scale: soft → HEAD, mixed → HEAD + index, hard → HEAD + index + working tree.
</details>

---

**Q4.** `git bisect` is used to
(a) split a commit in two
(b) binary-search the history for the commit that introduced a bug
(c) merge two branches
(d) split a large file

<details><summary>Answer</summary>

**(b).**

You mark a known-good and a known-bad commit, and git checks out midpoints for you to test. Across 1000 commits that is about **10 tests** rather than 1000 — the same binary search as in `01_DSA/04_Binary_Search`.

`git bisect run ./test.sh` automates it entirely, which is the version worth mentioning.
</details>

---

**Q5.** In the testing pyramid, which layer should be the **most numerous**?
(a) End-to-end  (b) Integration  (c) Unit  (d) Manual

<details><summary>Answer</summary>

**(c) Unit tests.**

They are fast, isolated and precise about what broke. Integration tests are fewer, and end-to-end tests fewest of all because they are slow and flaky.

**The inverted pyramid** — mostly e2e — is the classic anti-pattern: the suite becomes so slow that people stop running it, which defeats the point entirely.
</details>

---

**Q6.** A **mock** differs from a **stub** in that it
(a) returns real data
(b) additionally asserts how it was called
(c) is faster
(d) requires a database

<details><summary>Answer</summary>

**(b).**

A stub supplies canned answers so the test can proceed. A mock additionally *verifies the interaction* — that a method was called, with certain arguments, a certain number of times. A **fake** is a third thing: a working lightweight implementation, such as an in-memory repository.

**Over-mocking** couples tests to implementation details, so they break on every refactor even when behaviour is unchanged.
</details>

---

**Q7.** 100% code coverage guarantees
(a) no bugs  (b) every line ran during the tests  (c) all requirements are met  (d) good design

<details><summary>Answer</summary>

**(b).**

Coverage measures *execution*, not *verification*. A test suite that calls every line and asserts nothing reports 100% coverage and catches nothing.

Coverage is a useful **floor** (a sharp drop signals untested new code) and a terrible **target** (chasing the number produces assertion-free tests).
</details>

---

**Q8.** Which Scrum ceremony is for improving the **process** rather than the product?
(a) Sprint planning  (b) Daily standup  (c) Sprint review  (d) Retrospective

<details><summary>Answer</summary>

**(d) Retrospective.**

Planning decides what to build; the standup synchronises and surfaces blockers; the review demos the increment to stakeholders; the **retrospective** examines how the team worked and what to change.
</details>

---

**Q9.** Continuous **deployment** differs from continuous **delivery** in that
(a) delivery deploys every passing build automatically
(b) deployment deploys every passing build automatically; delivery only makes it deployable
(c) they are the same
(d) deployment skips testing

<details><summary>Answer</summary>

**(b).**

Continuous **delivery** means every passing build *could* be released, with the final push being a human decision. Continuous **deployment** removes that gate and ships automatically.

(d) is false and important: continuous deployment demands *more* automated testing, not less, because nothing else stands between a commit and production.
</details>

---

**Q10.** A **blue-green** deployment
(a) gradually replaces instances one at a time
(b) runs two identical environments and switches traffic between them
(c) sends a small percentage of traffic to the new version
(d) deploys only at night

<details><summary>Answer</summary>

**(b).**

Two full environments; traffic switches from blue to green at once, and rollback is a switch back — its main advantage. (a) is a **rolling** deployment; (c) is a **canary**.

Canary gives the best risk control (you see real errors on a small slice first); blue-green gives the fastest rollback; rolling is the cheapest in infrastructure.
</details>

---

**Q11.** A Docker container differs from a virtual machine in that it
(a) shares the host kernel rather than emulating hardware
(b) is always more secure
(c) cannot run Linux
(d) requires a hypervisor

<details><summary>Answer</summary>

**(a).**

A container isolates processes using kernel namespaces and cgroups, so it starts in milliseconds and costs megabytes. A VM boots a full guest OS on emulated hardware, costing gigabytes and seconds.

(b) is false — sharing a kernel is a *weaker* isolation boundary, which is precisely the security trade-off.
</details>

---

**Q12.** In a Dockerfile, copying `requirements.txt` and installing dependencies **before** copying the application source
(a) makes no difference
(b) exploits layer caching so a source change does not reinstall dependencies
(c) is required by Docker
(d) reduces the final image size

<details><summary>Answer</summary>

**(b).**

Docker caches each layer and invalidates everything after the first changed one. Installing dependencies before copying the source means an ordinary code change reuses the cached install layer, turning a two-minute build into a two-second one.

This is the most common Dockerfile review comment there is, and it is worth volunteering unprompted.
</details>

---

## Scoring

| Score /12 | Reading |
|---|---|
| 10+ | Fine — this subject is low-weight in OAs |
| 7–9 | Read `git-and-tools.md` once |
| < 7 | You will meet these in *interviews* more than OAs; work `rapid-fire-qa.md` |
