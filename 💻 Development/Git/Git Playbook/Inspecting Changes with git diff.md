---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - git-fundamentals
  - git-diff
  - working-directory
  - staging-area
  - mental-model
aliases:
  - git diff
  - git diff --staged
  - Comparing Commits
publish: true
---

# 📚 Inspecting Changes with git diff

> *`git diff` always compares two of Git's trees against each other — which two depends entirely on the flags you give it, and knowing that turns a confusing "why is diff showing nothing" moment into an obvious answer.*

---

## 🎯 Purpose

`git diff` is how you actually *see* what changed, at the line level, rather than just knowing that a file is "modified" from `git status`. But it's also one of the more confusing commands for beginners, because it quietly compares different pairs of trees depending on which flags you pass — and if you don't know which pair you're looking at, the output (or lack of it) seems to make no sense.

This note exists to make that pairing explicit, and to cover the handful of `diff` variants you'll reach for constantly: plain, `--staged`, comparing two arbitrary commits, and the `--stat` summary view.

---

## 🧠 Key Ideas

- `git diff` (no flags) compares the **working directory** against the **staging area** — "what have I changed that I haven't staged yet?"
- `git diff --staged` (alias: `--cached`) compares the **staging area** against the **repository** (`HEAD`) — "what have I staged that isn't committed yet?"
- `git diff <commit1> <commit2>` compares any two arbitrary commits (or branches, or tags) directly against each other — completely independent of your working directory or staging area.
- `git diff branch1 branch2` is a common variant of the above — see exactly what differs between two branches before merging.
- `--stat` shows a summary (files changed, insertion/deletion counts, a small bar chart) instead of full line-by-line output — great for a quick overview.
- This all maps directly onto [[Understanding the Three Trees]] — `diff` is fundamentally a tool for comparing two of those trees (or two arbitrary snapshots) against each other.

---

## ⚙️ How It Works

Every invocation of `git diff` is really asking: **compare snapshot A to snapshot B, and show me the line-level differences.** The flags just choose A and B:

| Command | Compares | Answers |
|---|---|---|
| `git diff` | working directory ↔ staging area | "What have I edited but not staged?" |
| `git diff --staged` | staging area ↔ repository (`HEAD`) | "What have I staged but not committed?" |
| `git diff HEAD` | working directory ↔ repository | "What's different from my last commit, staged or not?" |
| `git diff <c1> <c2>` | commit ↔ commit | "What changed between these two exact snapshots?" |

This is exactly why `git diff` can show "nothing" right after you run `git add` — the working directory now *matches* the staging area (you just copied it there), so there's no difference left to show for plain `diff`. The change hasn't disappeared; it's just moved to being visible under `--staged` instead.

Once you internalize "diff always needs two things to compare, and picks them based on the flags," the whole command family stops being a grab-bag of syntax to memorize.

---

## 💻 Examples

```bash
# --- Working directory vs staging area (the default) ---

echo "new line" >> notes.txt
git diff
# Shows the "new line" addition — it's in the working directory,
# not yet staged

git add notes.txt
git diff
# Shows NOTHING now — working directory matches staging area

# --- Staging area vs repository ---

git diff --staged
# Shows the "new line" addition — it IS staged, but not yet committed

git diff --cached
# Identical to --staged; --cached is just the older name for the same flag

# --- Working directory vs the last commit, regardless of staging ---

git diff HEAD
# Shows ALL uncommitted changes, staged or not, combined

# --- Comparing two arbitrary commits ---

git log --oneline
# a1b2c3d  Add checkout flow
# e4f5g6h  Add cart page

git diff e4f5g6h a1b2c3d
# Shows exactly what changed going from e4f5g6h to a1b2c3d

# --- Comparing two branches directly ---

git diff main feature/checkout
# What does feature/checkout have that main doesn't (and vice versa)?

# --- Quick summary instead of full diffs ---

git diff --stat HEAD~5 HEAD
#  src/app.js      | 42 ++++++++++++++----------
#  src/utils.js     |  8 +++----
#  README.md        |  3 +-
#  3 files changed, 33 insertions(+), 20 deletions(-)

# --- Scoping a diff to one file ---

git diff -- src/app.js
git diff --staged -- src/app.js
```

---

## 🚀 Real World Applications

- **Reviewing before committing** — `git diff --staged` right before `git commit` is a cheap habit that catches accidental leftover debug code or unintended changes.
- **Pre-merge review** — `git diff main feature-branch` shows exactly what a merge would introduce, before you actually merge it.
- **Quick "what did I just do" summary** — `git diff --stat HEAD~1` after a commit gives a fast sanity check of scope without a wall of line-by-line output.
- **Code review outside a PR tool** — comparing two commit hashes directly is useful when reviewing changes locally, e.g. before a teammate opens a pull request.
- **Auditing a release** — `git diff v1.2.0 v1.3.0 --stat` gives a quick shape of everything that changed between two tagged releases.

---

## ⚖️ Advantages

- Extremely precise — you always know exactly which two snapshots are being compared once you know the flags.
- Works on any two commits, branches, or tags — not limited to comparing against your current state.
- `--stat` gives a fast overview without needing to scroll through full diffs, and full diffs are always one flag away when you need detail.

---

## ⚠️ Limitations

- The "which two trees" mental model is not obvious from the command names alone — `--staged`/`--cached` in particular confuses newcomers.
- Diff output for binary files, or files with mixed line-ending changes, can be noisy or unhelpful without extra configuration.
- Very large diffs are still hard to read in a terminal — a GUI difftool or `--stat` first is often more practical than raw `-p`-style output for big changes.
- Comparing commits across unrelated branches with no common history can produce a diff that's technically correct but not meaningfully useful.

---

## 🚨 Common Mistakes

- Running `git add` and then being confused that `git diff` shows nothing — forgetting that the change moved to `--staged`, not that it vanished.
- Confusing `git diff` (working vs staging) with `git diff HEAD` (working vs last commit, combining staged and unstaged) — subtly different questions.
- Forgetting the `--` before a path when the filename could be ambiguous with a branch/commit name.
- Assuming `git diff branch1 branch2` shows a symmetric "what's unique to each branch" — it actually just shows the direct line differences between the two snapshots, which isn't the same thing as `git log branch1..branch2`'s commit-level view.
- Skipping `git diff --staged` before committing and only discovering an unintended change after it's already permanent history.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 2.3: Git Basics - Viewing the Commit History (and the surrounding sections on diffing)
- `git help diff`

---

## 💡 Wisdom from Mímir

`git diff --staged` right before every commit is one of the cheapest habits in all of Git, and one of the most skipped. It takes two seconds and it's the last checkpoint before something becomes permanent history. I've caught more stray `console.log`s and half-finished experiments with that one habit than with any linter.

---

## 🔗 Related Notes

- [[Understanding the Three Trees]] — the model that explains exactly what `diff` is comparing in each mode
- [[Committing Changes with git commit]] — the step `git diff --staged` is meant to precede
- [[Viewing History with git log]] — the complementary "when and by whom" view, alongside diff's "what exactly changed"
- [[Merging Branches]] — where comparing two branches with `diff` often happens before the actual merge
