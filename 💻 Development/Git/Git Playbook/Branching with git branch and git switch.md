---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - git-fundamentals
  - branching
  - git-switch
  - mental-model
aliases:
  - git branch
  - git switch
  - Creating Branches
publish: true
---

# <span class="rune">ᛟ</span> Branching with git branch and git switch

> *A branch is nothing more than a movable sticky note pointing at one commit — creating one is instant and nearly free, because it never copies a single file.*

---

## 🎯 Purpose

New Git users often picture branching the way file systems handle "Save As" — a full copy of the project, safely stashed off to the side. That mental model makes branching feel heavy and scary, and it's completely wrong for Git.

This concept exists to correct that: a branch is just a name attached to a commit hash. Once that clicks, branching stops feeling like a big decision and becomes what it's meant to be — a cheap, disposable way to try things.

---

## 🧠 Key Ideas

- A branch is a lightweight, movable pointer to a single commit — not a copy of the repository, not a copy of any files.
- Creating a branch (`git branch feature-x`) is just writing 40 characters (a commit hash) to a small file under `.git/refs/heads/` — essentially instantaneous no matter how large the repo is.
- `HEAD` is a pointer to "whatever branch (or commit) you currently have checked out" — it moves with you every time you switch branches.
- `git switch` (added in Git 2.23) is the newer, purpose-built command for changing branches — `git switch -c new-branch` creates and switches in one step.
- The older `git checkout -b new-branch` does the same job but is overloaded — `checkout` is also used for restoring files, which made its behavior confusing and error-prone.
- Deleting a branch (`git branch -d`) only deletes the pointer/sticky-note — the commits themselves stay in the repository until Git's garbage collector eventually cleans up anything truly unreferenced.

---

## ⚙️ How It Works

Picture your commit history as a chain of boxes, each one pointing back to its parent. A branch name is just a sticky note stuck on one box in that chain. `main` is a sticky note. `feature-login` is another sticky note, possibly on the very same box, or on a box further down a different path.

When you make a new commit, Git doesn't ask "which branch am I in" in some deep structural sense — it just looks at wherever `HEAD` currently points, creates a new commit box pointing back to the current one, and then moves *that branch's sticky note* forward to the new box. `HEAD` itself is really a pointer to a pointer — normally it points at a branch name (like `main`), and that branch name points at a commit. When you `switch` branches, you're just moving `HEAD` to point at a different sticky note; your working directory then updates to match whatever commit that sticky note is on.

This is why branching is instant: creating `feature-x` doesn't touch a single file in your working directory. It's only when you actually *switch to* it that Git updates your working files to match the commit that branch points at.

---

## 💻 Examples

```bash
# List all local branches (the * marks the one you're on)
git branch

# Create a new branch WITHOUT switching to it
git branch feature-login

# Old-school way to create AND switch in one step (still common, still works)
git checkout -b feature-login

# Modern equivalent — purpose-built, less overloaded, recommended for new work
git switch -c feature-login

# Switch to an existing branch
git switch main
# older equivalent: git checkout main

# See where HEAD currently points
cat .git/HEAD
# ref: refs/heads/feature-login

# Rename the branch you're currently on
git branch -m feature-login feature-user-login

# Delete a branch once it's merged (Git refuses if it isn't, protecting you)
git branch -d feature-login

# Force-delete a branch even if it has unmerged work (be sure first!)
git branch -D experimental-spike

# Create a branch starting from a specific commit or another branch,
# instead of from your current HEAD
git switch -c hotfix-1.2 v1.2.0
```

---

## 🚀 Real World Applications

- **Feature isolation**: each new feature, bugfix, or experiment gets its own branch, so `main` always stays deployable.
- **Throwaway experiments**: because creating a branch costs nothing, it's normal to branch off just to try a risky refactor, then delete it if it doesn't pan out.
- **Parallel work**: multiple people (or multiple tasks for one person) can work on independent branches without stepping on each other's uncommitted changes.
- **Hotfixes off a tag**: `git switch -c hotfix-1.2 v1.2.0` branches from a specific release point rather than the latest `main`, useful when you need to patch an older version still in production.
- **Code review workflows**: a pull/merge request is really just "please merge the commit my branch's sticky note points to."

---

## ⚖️ Advantages

- Branching is essentially free — no copying, no meaningful disk cost, no reason to hesitate.
- `git switch` separates "change branches" from "restore files," reducing the accidental-file-loss risk that came with overloaded `checkout`.
- Encourages small, isolated units of work instead of one long-lived branch everyone fights over.
- Deleting a branch is safe by default — `-d` refuses to delete unmerged work; you have to explicitly opt into `-D` to force it.

---

## ⚠️ Limitations

- `git switch` is relatively new (Git 2.23, 2019) — older tutorials, scripts, and some teams' muscle memory still default to `checkout -b`.
- A branch pointer only protects commits it can reach — if you delete a branch and never merged or tagged its tip, those commits become recoverable only via reflog until garbage collected.
- Branch names are local to your clone unless explicitly pushed (`git push -u origin feature-login`); a purely local branch is invisible to collaborators.
- Having *too many* long-lived branches (rather than short-lived feature branches) reintroduces exactly the "big scary merge" problem branching is meant to avoid.

---

## 🚨 Common Mistakes

- Treating a branch like a backup copy, then being surprised that uncommitted changes don't automatically travel with a `switch` — uncommitted work lives in the working directory, not on the branch pointer.
- Using `git checkout <name>` on autopilot and forgetting it can also silently discard file changes (`git checkout -- file.txt`) — a big part of why `switch`/`restore` were split out.
- Force-deleting (`-D`) a branch without checking whether it's merged, losing work that was never captured elsewhere.
- Forgetting to push a new local branch, then wondering why a teammate can't see it.
- Not realizing `HEAD` moved after a switch, then being confused why `git log` or files on disk look different than expected.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 3: Git Branching
- `git help switch`, `git help branch`
- Git 2.23 release notes — introduction of `git switch` and `git restore`

---

## 💡 Wisdom from Mímir

The single biggest unlock for new Git users is realizing that branching costs nothing. If you're hesitating to create a branch because it "feels like a big commitment," you're still thinking of it as a file-system copy. It isn't — it's a sticky note. Make the branch. Try the risky thing. If it's bad, delete the sticky note and walk away; the cost was one command in each direction.

---

## 🔗 Related Notes

- [[Understanding the Three Trees]] — how `switch` interacts with your working directory once HEAD moves
- [[Understanding HEAD and Detached HEAD State]] — what happens when HEAD points at a commit instead of a branch
- [[Merging Branches]] — bringing two branch pointers' histories back together
- [[Common Git Workflows - Feature Branch, Trunk-Based, and Git Flow]] — branching strategies built on top of this primitive
