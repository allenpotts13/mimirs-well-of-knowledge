---
publish: true
status: published
permalink: git/git-cheat-sheet
---

# <span class="rune">ᛊ</span> Git Cheat Sheet

> Dense reference across everyday Git. Each section links back to the full [[Git Codex|concept note]] for depth.

---

## Core Workflow

```bash
git status                     # what's staged, unstaged, untracked
git add file.txt                # stage a specific file
git add -p                       # stage interactively, hunk by hunk
git commit -m "message"          # commit staged changes
git diff                          # unstaged changes vs the last commit
git diff --staged                 # staged changes vs the last commit
git log --oneline --graph          # compact, visual history
git blame file.txt                  # who last touched each line, and when
```

See: [[Committing Changes with git commit]] · [[Inspecting Changes with git diff]] · [[Viewing History with git log]] · [[Tracing Changes with git blame]]

---

## Branching & Merging

```bash
git branch                       # list local branches
git branch new-feature            # create a branch (doesn't switch to it)
git switch new-feature             # switch to it
git switch -c new-feature           # create AND switch in one step

git merge feature-branch            # merge feature-branch INTO the current branch
git rebase main                      # replay current branch's commits ON TOP of main

git merge --abort                     # bail out of a conflicted merge
git rebase --abort                     # bail out of a conflicted rebase
```

| | Merge | Rebase |
|---|---|---|
| History | Preserves branch structure, adds a merge commit | Linear — rewrites commit history |
| Safety | Safe on shared/public branches | **Never** rebase a branch others have pulled |
| Result | An honest record of what happened | A cleaner, easier-to-read history |

See: [[Branching with git branch and git switch]] · [[Merging Branches]] · [[Rebasing vs Merging]] · [[Resolving Merge Conflicts]]

---

## Understanding State

```text
Working Directory → git add → Staging Area (Index) → git commit → Repository (.git)
```

```bash
git checkout abc1234              # DETACHED HEAD — not on any branch, just viewing a commit
git switch main                    # back to a real branch, HEAD re-attaches
git rev-parse HEAD                  # the current commit's full hash
```

See: [[Understanding the Three Trees]] · [[Understanding HEAD and Detached HEAD State]]

---

## Undoing & Rewriting History

```bash
git checkout -- file.txt           # discard UNSTAGED changes to a file (legacy syntax)
git restore file.txt                # discard UNSTAGED changes (modern syntax)
git restore --staged file.txt        # UNSTAGE a file, keep the changes in the working directory
git reset --soft HEAD~1               # undo last commit, keep changes STAGED
git reset --mixed HEAD~1               # undo last commit, keep changes UNSTAGED (default)
git reset --hard HEAD~1                 # undo last commit, DISCARD changes entirely — destructive

git revert abc1234                       # creates a NEW commit that undoes abc1234 — safe on shared history
```

**Reset rewrites history (danger on shared branches). Revert adds new history (safe anywhere).**

```bash
git rebase -i HEAD~3                # interactive rebase — squash, reword, reorder, drop commits
git commit --amend                    # fix the message or content of the LAST commit only

git cherry-pick abc1234                 # apply one specific commit onto the current branch

git reflog                               # the safety net — recovers "lost" commits after a bad reset/rebase
git reset --hard abc1234                  # restore to a commit found in the reflog

git push --force-with-lease               # safer force-push — fails if the remote has NEW commits you don't have
# git push --force                        # AVOID — silently overwrites others' work with no such check

git stash                                  # shelve uncommitted changes temporarily
git stash pop                               # reapply the most recent stash and remove it from the stack
git stash list                               # see all stashed changes
```

See: [[Undoing Changes - checkout, restore, and reset]] · [[Reverting a Commit vs Resetting]] · [[Squashing Commits]] · [[Interactive Rebase for Cleaning Up History]] · [[Cherry-Picking Commits]] · [[Recovering Lost Commits with the Reflog]] · [[Force-Pushing Safely with --force-with-lease]] · [[Stashing Changes with git stash]]

---

## Collaboration & Remotes

```bash
git fetch origin                  # download remote changes WITHOUT merging them in
git pull                            # fetch + merge (or rebase, if configured) in one step
git push origin main                 # upload local commits to the remote

git tag v1.0.0                        # lightweight tag on the current commit
git tag -a v1.0.0 -m "Release 1.0"      # annotated tag — has its own message, author, date
git push origin v1.0.0                    # tags don't push automatically — must push explicitly

git submodule add <url> path/            # embed another repo as a submodule
git submodule update --init --recursive    # pull in submodule content after cloning
```

```gitignore
# .gitignore — patterns for files Git should never track
node_modules/
*.log
.env
```

See: [[Working with Remotes - fetch, pull, and push]] · [[Tagging Releases]] · [[Git Submodules]] · [[Ignoring Files with .gitignore]]

---

## Workflows & Commit Messages

| Workflow | Shape |
|---|---|
| Feature Branch | One branch per feature, merged back into main when done |
| Trunk-Based | Small, frequent commits directly to (or very briefly off) main |
| Git Flow | Structured — `main`, `develop`, `feature/*`, `release/*`, `hotfix/*` |

```text
Good commit message shape:

Short summary line, imperative mood, under ~50 chars

Longer explanation of WHY this change was made, not just what
changed — the diff already shows what. Wrap around 72 chars.
```

See: [[Common Git Workflows - Feature Branch, Trunk-Based, and Git Flow]] · [[Writing Good Commit Messages]]

---

## 🔗 Related Notes

- [[Git Codex]]
- [[SQL Cheat Sheet]]
