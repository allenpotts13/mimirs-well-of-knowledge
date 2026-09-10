---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - git-reflog
  - data-recovery
  - undoing-changes
aliases:
  - git reflog
  - Reflog
  - Recovering Deleted Commits
publish: true
---

# <span class="rune">ᛟ</span> Recovering Lost Commits with the Reflog

> *The reflog is Git's private, local-only journal of everywhere HEAD has pointed — which means a commit is almost never actually gone, just temporarily out of sight.*

---

## 🎯 Purpose

You ran `git reset --hard` and watched three hours of work disappear. Or an interactive rebase went sideways and now half your commits seem to be missing. Or you deleted a branch before realizing it hadn't been merged. In every one of these moments, the instinctive panic is "I just destroyed my work" — and in the vast majority of cases, that panic is wrong.

The reflog exists precisely for this moment. It's Git quietly keeping a record of every commit HEAD has ever pointed to on your machine, so that "history-rewriting" operations are almost always recoverable, not truly destructive.

---

## 🧠 Key Ideas

- `git reflog` shows a chronological log of every position HEAD has occupied — every commit, every checkout, every reset, every rebase step, every branch switch.
- Commits that seem "lost" after `git reset --hard`, a botched rebase, or a deleted branch are usually still sitting in Git's object database, just unreachable from any branch — the reflog is what lets you find their hash again.
- The reflog is **strictly local** — it lives only in your own `.git` directory, was never pushed, and is not shared with collaborators or visible on the remote in any way.
- Reflog entries **expire**: by default, entries still reachable from a branch/HEAD are kept around 90 days, while entries for commits that have become fully unreachable are pruned sooner, around 30 days — after that, `git gc` may garbage-collect the underlying objects for good.
- The recovery pattern is simple: find the lost commit's hash in `git reflog`, then either `git checkout <hash>` to go look at it, or `git branch recovery-branch <hash>` to give it a permanent home again.

---

## ⚙️ How It Works

Every branch pointer and HEAD itself is really just a name pointing at a commit hash. Normally you only think about where they point *right now* — but Git has been quietly logging every place they've pointed, in order, the whole time. `git reflog` (short for "reference log") shows that history: `HEAD@{0}` is where you are right now, `HEAD@{1}` is where you were one HEAD-movement ago, and so on backward through resets, checkouts, commits, merges, and rebases.

This matters because operations like `git reset --hard` don't actually delete commit objects — they just move the branch pointer (and HEAD) somewhere else, leaving the old commits sitting in Git's object database, unreferenced by any branch, but very much still present on disk. An interactive rebase that goes wrong works the same way: the *original* commits still exist as objects, even though no branch points at them anymore after the rebase rewrites history. "Lost" really means "orphaned," not "deleted" — and orphaned objects stick around until Git's garbage collector eventually cleans them up, which is exactly why the reflog's expiration windows matter: they define your recovery window, not the objects' evaporation instantly on reset.

Because the reflog only exists locally, it's a personal safety net, not a shared one — it can't help you recover a commit a teammate lost on their machine, and it isn't a substitute for pushing important work to a remote.

---

## 💻 Examples

```bash
# The panic moment: this discards commits AND working directory changes
git reset --hard HEAD~3
# "Wait, I needed those commits!"

# Don't panic — check the reflog first
git reflog
# Output looks roughly like:
# a1b2c3d (HEAD -> main) HEAD@{0}: reset: moving to HEAD~3
# e4f5g6h HEAD@{1}: commit: Add validation logic
# i7j8k9l HEAD@{2}: commit: Fix edge case in parser
# m1n2o3p HEAD@{3}: commit: Initial draft of feature

# The commits are still right there — e4f5g6h is the tip you lost
# Option 1: just look at it / cherry-pick from it
git checkout e4f5g6h

# Option 2 (usually better): give it a real branch so it's not orphaned again
git branch recovery-branch e4f5g6h
git switch recovery-branch
# Your "lost" work is now safely on a normal branch again

# A botched interactive rebase — same idea, find the pre-rebase tip
git reflog
# Look for the entry just BEFORE the "rebase (start)" entries began
# e.g.: p9q8r7s HEAD@{7}: commit: Working state before the rebase

git branch pre-rebase-backup p9q8r7s
# Now you can compare, or reset back to it, without losing the rebase attempt either

# Recovering a deleted branch entirely
git branch -D feature/old-experiment
git reflog
# Find the last commit that branch pointed to before deletion
git branch feature/old-experiment <that-hash>
```

---

## 🚀 Real World Applications

- **Undoing an over-aggressive `git reset --hard`**: the single most common reflog rescue — recovering commits you thought were permanently discarded.
- **Recovering from a rebase that went wrong**: finding the pre-rebase commit to compare against, or abandon the rebase and start over from a known-good state.
- **Restoring an accidentally deleted branch**: `git branch -D` doesn't delete the commits, just the pointer — the reflog still remembers where it was.
- **Auditing "what did I actually do this afternoon"**: since the reflog logs every HEAD movement, it doubles as a detailed local activity log when you're trying to reconstruct a confusing sequence of operations.
- **Recovering an amended commit's previous version**: `git commit --amend` doesn't destroy the original commit either — its old hash is still findable in the reflog.

---

## ⚖️ Advantages

- Turns most "I think I just destroyed my work" moments into a quick, calm recovery instead of a real disaster.
- Requires no special setup or prior planning — it's on by default for every local repository.
- Gives a genuinely useful local audit trail of everywhere your branches and HEAD have been, independent of the "clean" commit history shown by `git log`.

---

## ⚠️ Limitations

- Entirely local — offers zero protection for work that was never committed locally in the first place, or for a collaborator's own local mistakes.
- Not permanent — entries (and the underlying objects, once fully unreachable) do eventually expire and get garbage-collected, by default around 90 days for reachable entries and roughly 30 days for unreachable ones.
- Doesn't help with uncommitted changes — if you never committed or stashed something, the reflog has nothing to recover, because there was never a commit to log in the first place.
- Reflog entries can get long and noisy on a very active repository, making the right entry occasionally tedious to spot by eye.

---

## 🚨 Common Mistakes

- Assuming a `reset --hard` or deleted branch is unrecoverable and giving up (or worse, redoing hours of work) without ever checking `git reflog` first.
- Waiting too long to recover something — while the window is generous (weeks to months), it isn't infinite, and running `git gc --aggressive` manually can shrink it further.
- Recovering a commit with `git checkout <hash>` and then doing more work in that detached-HEAD state without creating a branch — risking losing it all over again.
- Forgetting that the reflog is local-only, and expecting a teammate to be able to see or recover from *your* reflog remotely — they can't.
- Confusing `git reflog` (a log of HEAD/branch movements) with `git log` (a log of a branch's actual commit ancestry) — they answer different questions and can look confusingly similar.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 10.4: Git Internals - Maintenance and Data Recovery
- `git help reflog` — full command reference, including `git reflog expire` and per-branch reflogs

---

## 💡 Wisdom from Mímir

The single most reassuring sentence I can offer a panicking developer is: "Git almost never actually deletes anything immediately — it just stops pointing at it." `reset --hard`, a bad rebase, a deleted branch — these all just move pointers around while the underlying commit objects sit patiently in the object database, waiting for `git reflog` to hand you back the coordinates. Learn this tool once, calmly, before you need it in a panic — the five minutes it takes will save you many times that in future cold sweats.

---

## 🔗 Related Notes

- [[Understanding HEAD and Detached HEAD State]] — the reflog is fundamentally a log of HEAD's movements, so understanding HEAD makes reflog entries make sense
- [[Undoing Changes - checkout, restore, and reset]] — reflog is the safety net for exactly the operations described there
- [[Interactive Rebase for Cleaning Up History]] — the most common source of "wait, where did my commits go" moments the reflog rescues you from
