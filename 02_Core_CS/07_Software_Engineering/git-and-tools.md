# Git & Tools — Practical Reference

> Not theory — the commands and recoveries you will actually be asked about, plus what to say when an interviewer asks "walk me through your workflow".

---

## 1. Daily workflow

```bash
git switch -c feature/short-description   # branch off main
# ... work ...
git add -p                                # stage hunks selectively — review as you go
git commit -m "Add X so that Y"           # imperative mood, says WHY
git fetch origin
git rebase origin/main                    # linear history, before pushing
git push -u origin feature/short-description
# open a pull request
```

**Commit message form:** a short imperative subject (under ~50 chars), a blank line, then *why* the change was made and anything non-obvious. "Fix bug" tells a future reader nothing; "Reject negative quantities in the order API, which previously produced negative invoice totals" tells them everything.

---

## 2. Inspecting

```bash
git status                       # what is staged, modified, untracked
git log --oneline --graph --all  # the history as a picture
git log -p <file>                # how one file evolved
git diff                         # working tree vs index
git diff --staged                # index vs HEAD
git blame <file>                 # who last touched each line (and which commit)
git show <commit>                # a single commit in full
```

---

## 3. Undoing — the decision table

| Situation | Command |
|---|---|
| Unstage a file | `git restore --staged <file>` |
| Discard local changes to a file | `git restore <file>` |
| Amend the last (unpushed) commit | `git commit --amend` |
| Undo the last commit, keep changes staged | `git reset --soft HEAD~1` |
| Undo the last commit, keep changes unstaged | `git reset HEAD~1` |
| Undo the last commit, discard changes | `git reset --hard HEAD~1` ⚠️ |
| Undo a commit that is **already pushed** | `git revert <commit>` |
| Recover from a bad reset | `git reflog`, then `git reset --hard <sha>` |
| Shelve work temporarily | `git stash` / `git stash pop` |
| Apply one commit from another branch | `git cherry-pick <commit>` |

**The rule that matters:** anything that rewrites history (`reset`, `rebase`, `--amend`, force-push) is fine locally and dangerous on shared branches. Once a commit is public, undo it with `revert`, which adds a new commit rather than erasing an old one.

**`git reflog` is the safety net.** Almost nothing committed is truly lost for ~90 days — being able to say this in an interview is a small but real signal of experience.

---

## 4. Finding a regression with bisect

```bash
git bisect start
git bisect bad                 # current commit is broken
git bisect good v1.2.0         # this old tag was fine
# git checks out a midpoint; test it, then:
git bisect good      # or: git bisect bad
# ... repeat, ~log2(n) steps ...
git bisect reset
```
Even better, automate it: `git bisect run ./test.sh` finds the offending commit unattended. Across 1000 commits that is about **10 tests** instead of 1000 — the same binary search as `01_DSA/04_Binary_Search`, applied to history.

---

## 5. Resolving a merge conflict

```
<<<<<<< HEAD
your version
=======
their version
>>>>>>> feature-branch
```
1. Open the file and write the **intended** result — often neither side verbatim.
2. Delete all three marker lines.
3. `git add <file>`
4. `git commit` (merge) or `git rebase --continue` (rebase)

`git merge --abort` or `git rebase --abort` backs out entirely if you want to start over. Use `git diff --check` to catch leftover markers before committing — accidentally committing `<<<<<<<` is a genuinely common mistake.

---

## 6. Branching strategies

**Trunk-based:** everyone merges small changes into main daily, with incomplete work hidden behind feature flags. Requires good tests and CI; gives the fewest merge conflicts and the fastest feedback.

**Git Flow:** long-lived `develop`, `release/*` and `hotfix/*` branches alongside `main`. Suits scheduled releases and versioned products; accumulates merge pain when branches live for weeks.

**What to say:** name which one your team used and one problem it caused. "We used Git Flow, and the release branches drifted enough that merging back was a half-day job" is a much better answer than describing both models neutrally.

---

## 7. Docker essentials

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .                 # copy deps FIRST — layer caching
RUN pip install --no-cache-dir -r requirements.txt
COPY . .                                # source changes don't rebuild deps
CMD ["python", "main.py"]
```
```bash
docker build -t myapp:1.0 .
docker run -p 8080:8080 --env-file .env myapp:1.0
docker ps / docker logs <id> / docker exec -it <id> sh
```

**The layer-caching point is the one worth knowing:** copying `requirements.txt` and installing before copying the source means a code change does not reinstall dependencies. It is the most common Dockerfile review comment there is.

**Container vs VM:** a container shares the host kernel and isolates only the process namespace, so it starts in milliseconds and costs megabytes; a VM emulates hardware and boots a full OS.

---

## 8. What to have ready for the interview

- [ ] Your branching strategy, and one problem it caused
- [ ] How you write commit messages, with an example
- [ ] A time you resolved a nasty merge conflict
- [ ] A regression you found, and how (`bisect`, logs, a test)
- [ ] What your CI pipeline ran on each push
- [ ] The most useful code-review comment you have ever received
- [ ] One piece of technical debt you knowingly took on, and why

**Specificity is the whole game here.** "We used git" is worth nothing; "we ran trunk-based development with feature flags, and the flag cleanup was the part we consistently forgot" is worth a lot.
