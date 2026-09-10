---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - git-fundamentals
  - committing
  - history
aliases:
  - git commit
  - Amending Commits
publish: true
permalink: git/committing-changes-with-git-commit
---

# <span class="rune">ᛟ</span> Committing Changes with git commit

> *A commit is a permanent, named snapshot of whatever is currently in the staging area — the moment your work stops being "in progress" and becomes history.*

---

## 🎯 Purpose

Every other Git feature — branching, merging, `log`, `blame`, reverting — is built on top of commits. `git commit` exists to take the deliberate draft you built in the staging area and seal it into the repository as a permanent, addressable snapshot with a message explaining *why* it exists.

The command itself is simple. What trips people up is everything around it: how to write the message, when it's safe to change a commit you already made, and how to keep each commit meaningfully small.

---

## 🧠 Key Ideas

- `git commit` only commits what's **staged** — anything in the working directory that wasn't `git add`-ed is left out.
- `git commit -m "message"` writes an inline message without opening an editor — fast, good for short, single-line summaries.
- Running `git commit` with no `-m` opens your configured editor (`core.editor`) for a full message — better when you want a summary line *and* a wrapped body.
- `git commit --amend` replaces the most recent commit entirely — it doesn't add a second commit, it rewrites the last one (new content, new message, or both).
- Amending changes the commit's SHA. If that commit has already been pushed and others may have pulled it, amending creates a history mismatch that causes real pain for collaborators.
- Good commits are **atomic** — each one represents a single logical change, not a grab-bag of unrelated edits.

---

## ⚙️ How It Works

Think of `git commit` as pressing the shutter on a camera. The staging area is what's currently framed in the viewfinder; `commit` takes the picture and files it away, permanently, with a caption (the message). You can keep rearranging the scene (working directory) and framing (staging area) all you want — but once you press the shutter, that photo exists in the album forever, unaltered.

`--amend` is the one exception to "forever." It doesn't add a new photo to the album — it walks back, throws out the *last* photo, and takes a new one in its place, reusing whatever's currently staged plus (optionally) a new caption. This is fine when that photo is still just sitting in your camera roll (unpushed, local-only). It becomes a problem the moment you've already mailed prints to other people (pushed to a shared remote) — now you've swapped the picture out, but everyone else still has the original, and Git has no clean way to reconcile "your camera roll" with "their camera roll" without a forced overwrite.

---

## 💻 Examples

```bash
# Quick inline commit — good for small, self-explanatory changes
git add index.html
git commit -m "Fix broken nav link on homepage"

# No -m: opens your editor (vim/nano/VS Code/etc., set via core.editor)
# for a full summary line + blank line + wrapped body
git add .
git commit
# In the editor:
#   Add user authentication middleware
#
#   Validates JWT on every protected route and attaches the
#   decoded user object to req.user for downstream handlers.

# Forgot to include a file in your last commit? Stage it, then amend
git add forgotten-file.js
git commit --amend --no-edit
# --no-edit keeps the original commit message; omit it to edit the message too

# Fix a typo in your last commit's message without touching any files
git commit --amend -m "Fix typo in login validation error message"

# Check your editor before you get surprised by it
git config --global core.editor "code --wait"   # e.g. use VS Code

# See what --amend is about to do before you do it
git log -1 --stat
```

---

## 🚀 Real World Applications

- **Small hotfixes**: `-m` for a one-line commit when the change and reason are obvious and self-contained.
- **Feature work**: no `-m`, opening the editor to write a summary line plus a body explaining *why*, especially for non-obvious architectural decisions.
- **"Oops" moments**: `--amend --no-edit` right after committing, when you notice you forgot a file, left in a `console.log`, or need to re-run a linter/formatter before the commit "counts."
- **Cleaning up before a PR**: amending your latest local, unpushed commit to fix the message or squash in a tiny fixup before anyone else has seen it.
- **Code review discipline**: keeping commits atomic so a reviewer (or future you) can read `git log -p` and understand one idea per commit, instead of untangling a 40-file mega-commit.

---

## ⚖️ Advantages

- Commits are cheap and instant — there's no reason to hesitate to commit often.
- `--amend` gives you a clean way to fix mistakes without cluttering history with "fix typo" follow-up commits.
- Atomic commits make `git log`, `git bisect`, `git revert`, and `git blame` dramatically more useful.
- The editor-based flow (no `-m`) makes it natural to explain reasoning, not just restate the diff.

---

## ⚠️ Limitations

- `--amend` only touches the single most recent commit — fixing something further back requires interactive rebase instead.
- Amending rewrites the commit hash, which breaks anything that referenced the old hash (open PRs, CI runs pinned to a SHA, teammates' local branches).
- `-m` encourages short, single-line messages that skip the "why" — convenient, but often not enough context six months later.
- Committing isn't syncing — a commit only exists in your local repository until you `push` it.

---

## 🚨 Common Mistakes

- Running `git commit -m "..."` and being confused when files aren't included — they were never staged with `git add`.
- Amending a commit that's already been pushed and shared, then force-pushing over teammates' history without warning anyone.
- Cramming unrelated changes (a bug fix, a refactor, and a new feature) into one giant commit because it's "faster."
- Using `--amend` when you actually meant to make a *new* commit — accidentally erasing the previous one's distinct message/content.
- Forgetting `--no-edit` and accidentally opening the editor when you only meant to add a forgotten file, then panicking over how to exit vim.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 2: Git Basics, "Committing Your Changes"
- `git help commit`

---

## 💡 Wisdom from Mímir

`--amend` is one of the most useful commands in Git and also one of the most dangerous, and the line between the two is exactly one word: *pushed*. Before I amend anything, I ask myself "has anyone else possibly seen this commit?" If the answer is no, amend freely — it's just tidying your own desk. If the answer is yes, or even "maybe," reach for a new commit (or `git revert`) instead. The five seconds it takes to check saves the hour it takes to help a teammate untangle a diverged branch.

---

## 🔗 Related Notes

- [[Understanding the Three Trees]] — commit is the staging-area-to-repository step
- [[Writing Good Commit Messages]] — how to write the message half of this command well
- [[Undoing Changes - checkout, restore, and reset]] — for fixing commits further back than the most recent one
- [[Force-Pushing Safely with --force-with-lease]] — what you need once an amended commit has to be pushed
