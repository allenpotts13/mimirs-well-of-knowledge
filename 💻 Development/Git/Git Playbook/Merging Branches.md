---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - git-fundamentals
  - branching
  - merging
  - history
aliases:
  - git merge
  - Fast-Forward Merge
  - Three-Way Merge
publish: true
---

# <span class="rune">ᛟ</span> Merging Branches

> *Merging is how two diverging lines of history get woven back into one — sometimes by simply sliding a pointer forward, sometimes by creating a brand-new commit that remembers both parents.*

---

## 🎯 Purpose

Branches exist so people can work in isolation. Merging exists to end that isolation deliberately, on purpose, at a moment you choose — combining the changes from one branch into another. Understanding *how* Git actually performs a merge (not just that it "combines stuff") is what lets you predict what your history will look like afterward, and why `--no-ff` is sometimes worth reaching for on purpose.

---

## 🧠 Key Ideas

- A **fast-forward merge** happens when the branch you're merging *into* hasn't moved since the branch you're merging *from* diverged — Git just slides the pointer forward, no new commit is created.
- A **three-way merge** happens when both branches have new commits since they diverged — Git creates a new **merge commit** that combines both histories.
- A merge commit is special: it has **two parent commits** instead of one, which is exactly what lets `git log --graph` show the branch-and-rejoin shape.
- `git merge --no-ff` forces a real merge commit even when a fast-forward would be possible — useful when you want the historical record to show "this feature branch existed" rather than flattening it into a straight line.
- Merging doesn't move or rewrite any existing commits — it only ever adds new ones (the merge commit, if one is needed) and moves the current branch pointer.

---

## ⚙️ How It Works

Picture two people who started with the same paragraph of a document and both kept editing their own copy. A **fast-forward** is the easy case: only one of them actually changed anything — so "merging" is just handing back the version that changed, no comparison needed. Git detects this whenever the target branch (say `main`) is a direct ancestor of the branch being merged in (say `feature-x`): nothing on `main` diverged, so `main`'s pointer can just slide forward to `feature-x`'s latest commit.

A **three-way merge** is the real case: both people edited their own copies independently. To reconcile them, you need three things: your version, their version, and the shared original both started from (the merge base — the last commit both branches have in common). Git diffs each side against that common ancestor, combines non-overlapping changes automatically, and creates a new commit that has *two parents* — the tip of `main` and the tip of `feature-x` — recording "this is the point where these two histories rejoined." If both sides changed the *same* lines, that's a conflict, and Git pauses for you to resolve it by hand.

`--no-ff` matters because a fast-forward, while convenient, erases the *evidence* that a branch ever existed — the commits just appear to have been made directly on `main`. Forcing a merge commit preserves the shape: you can look at `git log --graph` later and see exactly which commits belonged to which feature, bundled under one merge point.

---

## 💻 Examples

```bash
# Set up: main hasn't moved since feature-x branched off
git switch main
git merge feature-x
# Fast-forward — main's pointer just slides up to feature-x's tip.
# No merge commit is created; git log looks perfectly linear.

# Same scenario, but force a merge commit anyway
git switch main
git merge --no-ff feature-x
# Creates a merge commit even though a fast-forward was possible,
# preserving "feature-x existed as its own line of work" in history.

# Real three-way merge: main has moved on since feature-x branched
git switch main
git merge feature-x
# Git creates a merge commit automatically because a straight
# fast-forward isn't possible — both branches have new commits.

# Inspect a merge commit's two parents
git log --oneline -1 --parents
# a1b2c3d  d4e5f6g h7i8j9k   <- the merge commit and its two parents

# Visualize the branch-and-rejoin shape
git log --graph --oneline --all

# Preview what a merge would touch before actually doing it
git merge --no-commit --no-ff feature-x
# Stages the merge result without committing — inspect with git status/diff,
# then either `git commit` to finish or `git merge --abort` to bail out
```

---

## 🚀 Real World Applications

- **Pull requests**: on GitHub/GitLab, clicking "Merge" is usually running `git merge` (or `--no-ff` merge, or squash, depending on the repo's configured merge strategy) on the server.
- **Preserving feature history**: teams that want to see "which commits belonged to which PR" in `git log --graph` configure `--no-ff` as the default merge behavior.
- **Release branches**: merging a `release/2.0` branch back into `main` after it's been tested and finalized.
- **Keeping a feature branch current**: periodically merging `main` *into* your feature branch to stay up to date with other people's changes while you keep working.
- **Long-lived integration branches**: in Git Flow-style workflows, `develop` accumulates merges from many feature branches before eventually merging into `main` for release.

---

## ⚖️ Advantages

- Merging never rewrites existing history — it's a purely additive, non-destructive operation, which makes it safe to use on shared/pushed branches.
- Fast-forward merges keep history perfectly linear when there's genuinely nothing to reconcile.
- `--no-ff` merge commits give you an honest, permanent record of when and how separate lines of work rejoined.
- Conflict resolution happens once, at the merge point — you don't have to resolve the same conflict repeatedly the way you sometimes do with rebase.

---

## ⚠️ Limitations

- A history full of merge commits can look noisy and hard to read compared to a clean, linear rebase-based history.
- Fast-forward merges lose the "this was a distinct feature" boundary unless you deliberately opt out with `--no-ff`.
- Merge commits with multiple parents make some tools (like `git bisect` or a simple `git log`) slightly more complex to reason about.
- A three-way merge conflict can be harder to resolve than a rebase conflict in some cases, because you're reconciling the *cumulative* difference between two long-diverged branches rather than one commit at a time.

---

## 🚨 Common Mistakes

- Assuming every merge creates a commit — fast-forwards don't, which surprises people expecting to see one in the log.
- Merging in the wrong direction (`git merge main` while on `feature-x` versus `git merge feature-x` while on `main`) and being confused about which branch actually moved.
- Not running `git status` mid-merge and missing that some files still have unresolved conflict markers before committing.
- Deleting a feature branch immediately after a fast-forward merge without realizing there's no separate merge commit tying its commits together — though the commits themselves are still safely part of `main`'s history.
- Merging a stale local branch without first pulling the latest `main`, creating avoidable conflicts that a quick `git pull` first would have sidestepped.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 3, "Basic Branching and Merging"
- `git help merge`
- GitHub Docs — "About merge methods on GitHub" (covers merge / squash / rebase merge strategies for PRs)

---

## 💡 Wisdom from Mímir

Fast-forward merges feel like a free win — no conflict, no merge commit, perfectly linear — but "free" is exactly why they quietly erase information. A year from now, "was this feature ever its own branch, or did someone just commit straight to main?" is a question `--no-ff` answers and a fast-forward doesn't. I don't force `--no-ff` on every merge, but on anything I'd call a "feature" rather than a one-line tweak, I want that boundary preserved in the permanent record.

---

## 🔗 Related Notes

- [[Branching with git branch and git switch]] — the pointers that merging brings back together
- [[Resolving Merge Conflicts]] — what to do when Git can't auto-combine both sides
- [[Rebasing vs Merging]] — the alternative approach to combining diverged history
- [[Viewing History with git log]] — reading merge commits and branch shape with `--graph`
