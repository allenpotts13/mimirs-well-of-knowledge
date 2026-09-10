---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - branching
  - merging
  - hotfix
  - history-rewriting
aliases:
  - git cherry-pick
  - Backporting a Commit
---

# 📚 Cherry-Picking Commits

> *`git cherry-pick` takes one specific commit from anywhere in the repository and replays its changes onto your current branch — a scalpel where `merge` is a whole transplant.*

---

## 🎯 Purpose

Sometimes you don't want an entire branch's worth of changes — you want exactly one commit's worth. A hotfix landed on `main` needs to also exist on `release/2.1`, but you don't want to merge all of `main`'s other in-progress work into that release branch. A teammate made one genuinely useful commit on an otherwise abandoned experimental branch, and you want just that piece.

`git cherry-pick` exists for exactly this: grabbing a single commit's changes and replaying them onto wherever you currently are, without dragging along everything else that commit's original branch contains.

---

## 🧠 Key Ideas

- `git cherry-pick <commit-hash>` applies the changes introduced by that one commit onto your current branch, creating a **new commit** with a new hash (same content, different parent, different position in history).
- It's selective by design — unlike `merge`, which brings in an entire branch's history, cherry-pick brings in exactly the commit(s) you name and nothing else.
- The most common real-world use is **backporting**: taking a fix from a main development branch and applying it to an older release branch that still needs the fix.
- Cherry-picking can produce **merge conflicts**, just like a regular merge, if the target branch has diverged from the context the original commit was made in.
- You can cherry-pick a range of commits (`git cherry-pick A..B`) or multiple individual commits in one command.
- `--no-commit` (or `-n`) applies the changes to your working directory/staging area without immediately creating a commit, useful when you want to combine or tweak the result first.

---

## ⚙️ How It Works

Under the hood, cherry-pick computes the *diff* that a given commit introduced (essentially: what changed between that commit and its parent), and then applies that diff onto your current branch as a new change, followed by creating a new commit with the same message (by default) but a fresh hash and fresh parent.

This is why cherry-picked commits look like a copy of the original in `git log` — same author, same message, same content — but are, mechanically, a completely separate commit object. Git even records a "cherry picked from commit ..." line in the new commit's message by default when you use `-x`, which is worth doing on shared branches so it's traceable later.

Because it's just "compute a diff, then apply it," anything that could make an ordinary patch fail to apply cleanly (the surrounding code has changed too much since the original commit) will cause the same conflict-resolution process as a merge — Git pauses, marks the conflicted files, and waits for you to resolve them the same way you would in [[Resolving Merge Conflicts]].

---

## 💻 Examples

```bash
# --- The classic backport scenario ---

git log --oneline main
# a1b2c3d  Fix critical null-pointer bug in checkout
# ...

git switch release/2.1
git cherry-pick a1b2c3d
# The fix commit is now replayed onto release/2.1 as a new commit,
# without pulling in any of main's other, unrelated work

git push origin release/2.1

# --- Cherry-picking with traceability ---

git cherry-pick -x a1b2c3d
# Appends "(cherry picked from commit a1b2c3d...)" to the commit message —
# useful so anyone reading history later knows where it came from

# --- Cherry-picking multiple commits ---

git cherry-pick a1b2c3d e4f5g6h
# Applies both commits, in the order given, as two new commits

# --- Cherry-picking a range ---

git cherry-pick h7i8j9k..a1b2c3d
# Applies every commit AFTER h7i8j9k up through a1b2c3d (exclusive/inclusive
# boundaries work the same as with git log ranges)

# --- Handling a conflict during cherry-pick ---

git cherry-pick a1b2c3d
# CONFLICT (content): Merge conflict in src/checkout.js
# error: could not apply a1b2c3d... Fix critical null-pointer bug

# Resolve the conflict manually in the file, then:
git add src/checkout.js
git cherry-pick --continue

# Or abandon the cherry-pick entirely:
git cherry-pick --abort

# --- Applying changes without auto-committing ---

git cherry-pick -n a1b2c3d
# Changes are staged but not committed — lets you combine with other
# edits or amend before finalizing
```

---

## 🚀 Real World Applications

- **Backporting a hotfix** — a critical bug fix committed to `main` needs to also ship in a currently-supported `release/x.y` branch without merging all of `main`.
- **Rescuing one commit from an abandoned branch** — a branch has one genuinely good commit buried among experiments that never worked out; cherry-pick just that one instead of merging the whole thing.
- **Splitting up an over-broad branch** — if a feature branch accidentally accumulated unrelated commits, cherry-picking the relevant ones onto a clean branch can untangle it.
- **Applying a fix across multiple long-lived version branches** — e.g. a security patch that needs to land on `release/1.x`, `release/2.x`, and `main` independently.
- **Promoting a specific commit through an environment pipeline** — in some workflows, individual approved commits are cherry-picked forward from a staging branch to a production branch rather than merging everything.

---

## ⚖️ Advantages

- Extremely precise — brings in exactly one change, with none of a branch's other unrelated history.
- Doesn't require merging entire branches together, which keeps release branches clean and minimal.
- Works well for urgent, narrowly-scoped fixes that need to reach multiple places quickly.

---

## ⚠️ Limitations

- Creates a genuinely new, separate commit — the original and the cherry-picked copy have different hashes, which can make history harder to trace without `-x`.
- Can produce the same conflicts a merge would, and resolving them requires the same care.
- Overused, it leads to duplicated logic scattered across branches instead of a clean, traceable history — it's a tool for exceptions, not a substitute for proper merging or rebasing.
- Cherry-picking a commit that itself depends on earlier commits not yet present on the target branch can fail or produce broken code, even without a literal text conflict.

---

## 🚨 Common Mistakes

- Cherry-picking a commit onto a branch that's missing prerequisite earlier commits, resulting in code that applies cleanly but doesn't actually work.
- Forgetting `-x`, then later having no way to trace where a commit on a release branch actually originated from.
- Using cherry-pick as a routine way to move work between branches instead of merging or rebasing properly — it doesn't scale to "bring over all my changes," only to "bring over this one specific thing."
- Not running `--abort` when a cherry-pick conflict turns out to be more trouble than it's worth, leaving the repository in a half-finished cherry-pick state.
- Cherry-picking a merge commit without the `-m` parent-selection flag (the same gotcha `git revert` has) and getting confusing or incorrect results.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 5.3: Distributed Git - Contributing to a Project (cherry-pick used in patch workflows)
- `git help cherry-pick`

---

## 💡 Wisdom from Mímir

Cherry-pick is a scalpel, not a workflow. It's the right tool exactly once in a while — a hotfix that needs to exist in two places, a good commit stranded on a dead branch — and the wrong tool the moment it becomes a habit for routinely moving work around. If you find yourself cherry-picking the same set of commits onto multiple branches over and over, that's usually a sign your branching strategy, not your cherry-picking skill, needs attention.

---

## 🔗 Related Notes

- [[Resolving Merge Conflicts]] — the same conflict-resolution process applies when a cherry-pick doesn't apply cleanly
- [[Merging Branches]] — the "bring in everything" counterpart to cherry-pick's "bring in one thing"
- [[Reverting a Commit vs Resetting]] — another commit-level operation, but for undoing rather than replaying
- [[Common Git Workflows: Feature Branch, Trunk-Based, and Git Flow]] — where release-branch backporting patterns like this typically fit
