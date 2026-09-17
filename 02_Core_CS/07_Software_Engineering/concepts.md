# Software Engineering & Tools — Concepts

## 1. Core idea in 3 lines
This folder carries almost no OA weight and a surprising amount of *interview* weight, because every conversation about your projects touches it: how you branched, how you tested, how you found a bug. The theory is thin; what matters is that you can describe your actual practice concretely. An honest, specific answer here beats a comprehensive but generic one every time.

---

## 2. Git — the part that actually gets asked

**The three areas:** working directory → staging area (index) → repository. `git add` moves changes from the first to the second; `git commit` from the second to the third.

**Merge vs rebase.**
- `git merge` creates a merge commit joining two histories. It preserves exactly what happened, at the cost of a noisier graph.
- `git rebase` replays your commits on top of another branch, producing a **linear** history — but it rewrites commit hashes.
- **The golden rule: never rebase a branch that others have pulled.** Rewriting shared history forces everyone else into a painful recovery.

**Reset vs revert vs checkout.**

| Command | Effect | Safe on shared history? |
|---|---|---|
| `git reset --soft` | move HEAD, keep index and working tree | rewrites history — no |
| `git reset --mixed` (default) | move HEAD, reset index, keep working tree | no |
| `git reset --hard` | move HEAD, discard index and working tree | no, and destructive locally |
| `git revert` | create a **new** commit undoing an old one | **yes** |
| `git checkout` / `git switch` | change branch or restore files | yes |

**`revert` is the answer whenever the commit is already pushed.** `reset` is for local cleanup only.

**Other commands worth owning:** `git stash` (shelve work in progress), `git cherry-pick` (apply one commit elsewhere), `git bisect` (binary search the history for the commit that introduced a bug — a genuinely impressive answer to "how do you find a regression"), `git reflog` (recover from a bad reset), `git log --oneline --graph`.

**Resolving a conflict:** git marks the regions, you edit the file to the intended result, remove the markers, `git add` it, then `git commit` (or `git rebase --continue`). The important part is that *you* decide the correct content — git never can.

**Branching strategies:** trunk-based development (short-lived branches, merged daily, behind feature flags) versus Git Flow (long-lived develop/release/hotfix branches). Trunk-based dominates at companies practising continuous delivery, because long-lived branches accumulate merge pain.

---

## 3. Testing

**The pyramid:** many fast unit tests, fewer integration tests, very few end-to-end tests. Inverting it — mostly slow, flaky e2e tests — is the standard failure mode, because such a suite is slow enough that people stop running it.

| Level | Scope | Speed | Fragility |
|---|---|---|---|
| **Unit** | one function or class, dependencies faked | milliseconds | low |
| **Integration** | several components together, real DB or API | seconds | medium |
| **End-to-end** | the whole system through its real interface | minutes | high |

**Test doubles:** a **stub** returns canned answers; a **mock** additionally asserts *how* it was called; a **fake** is a working lightweight implementation (an in-memory repository). Over-mocking produces tests that verify your implementation rather than your behaviour, and that break on every refactor.

**Good tests are:** deterministic (no clock, no network, no random without a seed), isolated (any order, any subset), fast, and focused on **behaviour rather than implementation**. A test that breaks when you rename a private method is testing the wrong thing.

**TDD** is red → green → refactor: write a failing test, make it pass minimally, then clean up. Its real benefit is design pressure — code that is hard to test is usually badly coupled.

**Coverage** measures which lines ran, not whether they were *checked*. High coverage with weak assertions is worthless; it is a useful floor and a terrible target.

**Regression testing** guards previously fixed bugs. Every bug fix should arrive with a test that fails before the fix and passes after — that sentence alone is a strong interview answer.

---

## 4. SDLC and process

| Model | Shape | Suits |
|---|---|---|
| Waterfall | sequential phases, no going back | fixed, well-understood requirements |
| V-model | waterfall with a test phase mirroring each stage | safety-critical, regulated work |
| Iterative / incremental | build in repeated cycles | most real projects |
| **Agile / Scrum** | short sprints, continuous feedback | changing requirements |
| Spiral | risk-driven iterations | large, high-risk systems |

**Scrum in practice:** a 1–4 week sprint, with sprint planning, a daily standup (what I did, what I will do, what is blocking me), a review (demo) and a retrospective (process improvement). Roles: product owner (what and why), scrum master (process), development team (how).

**What to actually say about agile:** describe how your team worked, not the textbook ceremony list. "We ran two-week sprints, and standups were useful for unblocking but tended to become status reports" is a much better answer than reciting the Scrum guide.

---

## 5. CI/CD and deployment

**Continuous integration:** every push triggers an automated build and test run, so integration problems surface in minutes rather than at a release.
**Continuous delivery:** every passing build is *deployable*. **Continuous deployment:** every passing build is actually deployed.

**A typical pipeline:** lint → build → unit tests → integration tests → security scan → artifact build → deploy to staging → smoke tests → deploy to production.

**Deployment strategies:** blue-green (two identical environments, switch traffic, instant rollback), canary (route a small percentage of traffic to the new version first), rolling (replace instances gradually), feature flags (ship the code dark and enable it separately, which decouples deploy from release).

**Docker** packages an application with its dependencies into an image, so the same artifact runs identically everywhere — it solves "works on my machine". A container shares the host kernel, which is why it is far lighter than a VM. **Kubernetes** orchestrates many containers: scheduling, scaling, service discovery, self-healing.

---

## 6. Code quality and review

**What makes code readable:** names that state intent, small functions with one job, shallow nesting (early returns over deep `if` chains), and consistency with the surrounding code. Comments should explain **why**, not what — if you need a comment to explain *what*, rename something instead.

**DRY** means one authoritative source for each piece of *knowledge*, not banning similar-looking lines. Two functions that look alike but change for different reasons should stay separate; deduplicating them couples two unrelated things.

**YAGNI** — do not build for imagined future requirements; the guess is usually wrong and the abstraction gets in the way.

**Code review** is for correctness, edge cases, security, readability and knowledge sharing — not style, which a formatter should handle automatically. Good review comments are specific and non-personal: "this loop re-queries per row; a join would avoid the N+1" rather than "this is inefficient".

**Technical debt** is a deliberate trade: shipping a shortcut now and paying interest later in slower changes. It becomes a problem when it is unacknowledged and unbudgeted, not when it exists.

---

## 7. Debugging methodology

The method matters more than the tools, and this is a genuinely common interview question.

1. **Reproduce it reliably.** A bug you cannot reproduce cannot be verified as fixed.
2. **Isolate it.** Binary-search the input, the code path, or the history (`git bisect`).
3. **Form a hypothesis** that predicts something you have not yet observed.
4. **Test the hypothesis** with one change at a time.
5. **Fix the cause, not the symptom.** A null check that hides a bad state is not a fix.
6. **Add a regression test** that fails before the fix and passes after.
7. **Ask why it was not caught** — missing test, missing type, missing review.

**Logging vs breakpoints:** breakpoints for a local, reproducible bug; structured logging for production, concurrency and distributed systems, where stopping the world is not possible. Log at boundaries — request in, request out, external calls — with correlation IDs to tie a request together.

**Rubber-duck debugging** works because explaining the code forces you to articulate the assumption you have not checked.

---

## 8. Recall questions

1. Explain merge vs rebase and the rule about shared branches.
2. When do you use `revert` instead of `reset`?
3. What does `git bisect` do?
4. Describe the testing pyramid and the usual failure mode.
5. Stub vs mock vs fake?
6. What is wrong with treating coverage as a target?
7. What are the Scrum ceremonies and roles?
8. Distinguish continuous integration, delivery and deployment.
9. Compare blue-green, canary and rolling deployments.
10. Why is a container lighter than a VM?
11. What does DRY actually mean, and when does deduplication hurt?
12. Give your debugging methodology in seven steps.
