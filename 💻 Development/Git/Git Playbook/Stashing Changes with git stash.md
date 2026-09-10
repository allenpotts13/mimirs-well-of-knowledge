---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - git-fundamentals
  - working-directory
  - context-switching
aliases:
  - git stash
  - Stash Stack
  - git stash pop
publish: true
permalink: git/stashing-changes-with-git-stash
---

# <span class="rune">ᛟ</span> Stashing Changes with git stash

> *A stash is a shelf where you can set down unfinished work mid-thought, walk away to do something else, and come back later to pick it up exactly where you left off.*

---

## 🎯 Purpose

Sometimes you're mid-change — working directory dirty, nothing worth committing yet — and something else demands your attention: an urgent bug needs a branch switch, or you need a clean working directory to pull the latest `main`. Committing half-finished work just to switch branches pollutes history with garbage commits. `git stash` exists for exactly this: it takes your uncommitted changes off to the side, restores a clean working directory, and lets you bring those changes back whenever you're ready.

---

## 🧠 Key Ideas

- A stash captures both the working directory and staging area's current state and sets it aside, returning your working directory to match the last commit (clean).
- The stash is a **stack**, not a single slot — you can stash multiple times in a row, and each stash gets pushed onto that stack (`stash@{0}` is the most recent, `stash@{1}` the one before it, and so on).
- `git stash pop` applies the most recent stash *and removes it* from the stack — good when you're confident you're done with it.
- `git stash apply` applies the most recent stash but *leaves it* on the stack — good when you might want to apply the same stashed changes to more than one branch.
- By default, `git stash` only stashes tracked, modified files — `git stash -u` (or `--include-untracked`) also stashes brand-new untracked files.
- `git stash push -m "message"` lets you name a stash, which matters enormously once you have more than one sitting on the stack at a time.

---

## ⚙️ How It Works

Think of the stash as a small, private undo-stack that lives alongside your commit history but isn't part of it. When you run `git stash`, Git essentially makes a temporary commit-like snapshot of your working directory and staging area, remembers it in this separate stack, and then reverts your working directory back to match `HEAD` — as if you'd never touched anything.

Because it's a genuine stack (last in, first out), stashing a second time doesn't overwrite the first stash — it sits on top. `stash@{0}` always refers to whatever you most recently stashed; `stash@{1}` is the one before that. This is the detail that trips people up most: many assume there's only one stash "slot," then panic when a second `git stash` seems to have "lost" the first one. It didn't — it's still there, one level down, exactly where `git stash list` will show it.

`pop` and `apply` differ only in cleanup: `pop` = apply the changes, *then* remove that entry from the stack, as if you'd never needed the shelf at all once you're back holding the work. `apply` = apply the changes but leave the shelf entry standing, useful when you want to try those same changes on a different branch too, or you're not yet fully confident the apply will go cleanly and want a fallback still sitting there.

---

## 💻 Examples

```bash
# Working directory is dirty, but you need to switch branches for an urgent fix
git status
# modified: app.js (uncommitted, not ready to commit yet)

git stash
# Saved working directory and index state WIP on feature-x: a1b2c3d ...
# Working directory is now clean

git switch main
# ... fix the urgent bug, commit, push ...
git switch feature-x

git stash pop
# Re-applies app.js's changes AND removes this entry from the stash stack

# Stashing more than one thing — it's a stack, not a single slot
git stash push -m "half-done validation refactor"
git stash push -m "experimental caching attempt"

git stash list
# stash@{0}: On feature-x: experimental caching attempt
# stash@{1}: On feature-x: half-done validation refactor

# Apply a SPECIFIC stash by reference, not just the most recent
git stash apply stash@{1}
# Applies "half-done validation refactor" but leaves it on the stack too,
# in case something goes wrong and you need to try again

# Include untracked (brand new) files too — off by default!
git stash -u
# or the long form:
git stash push --include-untracked

# See what's actually inside a stash before applying it
git stash show -p stash@{0}

# Done with a stash and don't need it anymore
git stash drop stash@{1}

# Nuke the entire stash stack (careful — this is permanent)
git stash clear
```

---

## 🚀 Real World Applications

- **Urgent context switches**: stashing half-finished feature work to jump onto a critical hotfix branch, then popping it back once the fix ships.
- **Pulling into a dirty working directory**: `git pull` refuses when you have uncommitted changes that would conflict — stash, pull, pop is the standard fix.
- **Testing an idea without committing to it**: stashing your in-progress change to see how the codebase behaves *without* it, then popping it back.
- **Juggling multiple unrelated tinkering sessions**: naming stashes with `-m` so you can tell "half-done validation refactor" apart from "experimental caching attempt" days later.
- **Trying the same uncommitted change on two branches**: `apply` (not `pop`) so the stash survives being applied more than once.

---

## ⚖️ Advantages

- Lets you switch context instantly without committing unfinished, broken, or embarrassing work-in-progress.
- The stack model means you're never limited to "one thing set aside at a time."
- Named stashes (`push -m`) make a multi-stash workflow perfectly manageable instead of a mystery pile.
- Nothing is lost — `apply` keeps a safety copy, and even `drop`ped/`pop`ped stashes are typically recoverable via the reflog for a while.

---

## ⚠️ Limitations

- Untracked and ignored files are excluded by default, which can surprise people expecting *everything* dirty to be swept up (`-u` fixes untracked; ignored files need `-a`/`--all`).
- Stashes aren't pushed to a remote — they're purely local, so they don't help you hand off in-progress work to a teammate.
- A large or old stash pile is easy to forget about entirely — `git stash list` is worth checking periodically.
- Applying a stash can conflict, just like a merge, if the branch has changed significantly since you stashed — conflict markers appear and need the same resolution process.

---

## 🚨 Common Mistakes

- Assuming there's only one stash slot, then being confused when a second `git stash` seems to "overwrite" the first — it's a stack; `git stash list` shows everything.
- Using `pop` and then hitting a conflict, which leaves the stash *not* automatically dropped (Git keeps it on the stack until the conflict is resolved) — forgetting this and later wondering why an old stash reappears.
- Forgetting `-u` and being surprised that new files didn't come along for the ride.
- Letting stashes pile up nameless (`git stash` with no `-m`) until `git stash list` is an unreadable wall of "WIP on feature-x" entries.
- Treating the stash as a substitute for committing — it's a short-term shelf, not a backup strategy; a truly important half-finished change is often safer as a real (if messy) commit on a scratch branch.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 7.3, "Git Tools: Stashing and Cleaning"
- `git help stash`

---

## 💡 Wisdom from Mímir

The single habit that saves the most confusion with stashing is naming them. `git stash` with no message works fine when you're popping it back thirty seconds later — but the moment more than one thing is on that shelf, "WIP on feature-x" tells you nothing. `git stash push -m "half-done validation refactor"` costs three extra words and saves you from ever having to `git stash show -p` your way through a guessing game.

---

## 🔗 Related Notes

- [[Understanding the Three Trees]] — what stashing actually captures (working directory + staging area)
- [[Undoing Changes - checkout, restore, and reset]] — related but distinct ways of discarding or moving uncommitted changes
- [[Branching with git branch and git switch]] — stashing is most often used right before a branch switch
- [[Recovering Lost Commits with the Reflog]] — recovering a dropped or cleared stash if needed
