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
  - staging-area
  - mental-model
aliases:
  - Working Directory vs Staging vs Repository
  - Git's Three States
  - The Index
publish: true
---

# <span class="rune">ᛟ</span> Understanding the Three Trees

> *Every tracked file exists in up to three places at once — the working directory, the staging area, and the repository — and nearly every confusing Git command is really just moving content between them.*

---

## 🎯 Purpose

Beginners usually learn Git as a list of commands to memorize: `add`, `commit`, `status`, `diff`. That works until something goes wrong — "I committed but my changes aren't there," "why does `diff` show nothing?" — and the memorized commands stop making sense.

This concept exists to replace memorization with a mental model. Once you can picture the three trees, most Git commands become "move content from tree A to tree B" instead of magic incantations.

---

## 🧠 Key Ideas

- **Working Directory** — the actual files on disk, exactly as you see them in your editor. Freely editable, not yet tracked by any Git operation.
- **Staging Area** (a.k.a. "the index") — a draft of your *next* commit. Not the same as your working files, and not yet permanent history.
- **Repository** (`.git`) — the permanent, versioned history. Once something is committed here, it has a durable snapshot.
- `git add` copies content from the **working directory** into the **staging area**.
- `git commit` copies content from the **staging area** into the **repository** as a new snapshot.
- `git checkout` / `git restore` can pull content back *out* of the repository or staging area into the working directory.

---

## ⚙️ How It Works

Think of writing a physical letter:

1. You write a draft on paper (**working directory**) — you can scribble, cross out, rewrite freely.
2. You copy the final wording onto a clean sheet to mail (**staging area**) — this is what you've decided *is* the final version, even if you keep editing the scratch paper.
3. You seal it in an envelope and mail it (**repository**, via `commit`) — now it's permanent and dated.

`git status` is just Git telling you the differences between these three trees: what's changed in your working directory that isn't staged yet, and what's staged that isn't committed yet.

---

## 💻 Examples

```bash
# Edit a file — this only changes the working directory
echo "console.log('hi')" >> app.js

git status
# Shows app.js as "modified" — different from what's in the repository

git diff
# Shows the exact lines that differ between working directory and repository

git add app.js
# Copies the current content of app.js into the staging area

git status
# Now shows app.js as "staged" — no longer just a working-directory change

git diff
# Shows nothing now — working directory matches staging area

git diff --staged
# Shows the staged change — the difference between staging area and repository

git commit -m "Add hello log"
# Copies the staged snapshot into the repository as a permanent commit

git status
# Clean — working directory, staging area, and repository all match
```

---

## 🚀 Real World Applications

- **Partial commits**: `git add -p` lets you stage only some of the changes in a file, building a clean, single-purpose commit even when you edited multiple things at once.
- **Reviewing before committing**: `git diff --staged` lets you double-check exactly what's about to become permanent history before you commit it.
- **Debugging "my commit is empty" or "nothing to commit"**: almost always a staging-area misunderstanding — the fix is knowing which tree actually has the change you expect.

---

## ⚖️ Advantages

- Staging lets you build a deliberate, curated commit instead of committing every unrelated change at once.
- Encourages small, focused commits, which makes `git log` and `git blame` far more useful later.
- Gives you a review checkpoint (`git diff --staged`) before anything becomes permanent.

---

## ⚠️ Limitations

- It's an extra step beginners often forget, leading to "I edited the file, why didn't it commit?" confusion.
- Some GUI tools and `git commit -a` auto-stage tracked files, which hides the model and can make it harder to learn.
- New (untracked) files are never auto-staged by `-a` — only previously tracked, modified files are.

---

## 🚨 Common Mistakes

- Editing a file and running `git commit` without `git add` first, then being confused that the change isn't in history.
- Assuming `git commit -a` stages everything — it skips brand-new untracked files entirely.
- Confusing `git diff` (working directory vs. staging) with `git diff --staged` (staging vs. repository) — they answer different questions.
- Thinking "staged" means "committed" — a staged change is still fully reversible and not yet permanent.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 2: Git Basics
- `git help status`, `git help diff`, `git help add`

---

## 💡 Wisdom from Mímir

Whenever a Git command's behavior seems surprising, I stop and ask one question: *which of the three trees is this actually touching?*

`checkout` touching the working directory behaves very differently from `checkout` touching a branch pointer. `reset` can touch just the repository pointer, or the staging area too, or the working directory as well, depending on `--soft`, `--mixed`, or `--hard`. The commands aren't inconsistent — they're just each choosing a different subset of the three trees to affect. Once that clicks, the rest of Git stops feeling like memorization.

---

## 🔗 Related Notes

- [[Undoing Changes - checkout, restore, and reset]] — the `--soft`/`--mixed`/`--hard` distinction only makes sense in terms of these three trees
- [[Committing Changes with git commit]] — the staging-area-to-repository step in detail
- [[Inspecting Changes with git diff]] — reading the differences between the three trees
- [[Stashing Changes with git stash]] — temporarily setting aside working-directory and staging-area changes
