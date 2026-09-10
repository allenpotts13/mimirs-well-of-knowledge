---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - git-fundamentals
  - head
  - detached-head
  - mental-model
  - branches
aliases:
  - Detached HEAD
  - What is HEAD in Git
publish: true
---

# 📚 Understanding HEAD and Detached HEAD State

> *HEAD is just a pointer to "where you currently are" in the commit graph — normally it points at a branch name, which points at a commit, but you can also point it straight at a commit, and that one change explains an entire category of "wait, where did my commits go?" panic.*

---

## 🎯 Purpose

`HEAD` shows up constantly in Git's output and vocabulary — `HEAD~1`, `HEAD^`, "you are in 'detached HEAD' state" — but it's rarely explained clearly, so people learn to pattern-match around it without understanding it. That's fine right up until you check out a specific commit (to look at old code, bisect a bug, or inspect a tag) and Git greets you with a slightly alarming warning about a "detached HEAD."

Understanding what HEAD actually is turns that warning from scary into completely unsurprising — and tells you exactly what to do (and what *not* to do) while you're in that state.

---

## 🧠 Key Ideas

- `HEAD` is a pointer that answers one question: **"what commit am I currently looking at / would my next commit be based on?"**
- Normally, `HEAD` points to a **branch** (e.g. `main`), and that branch pointer points to a commit. Moving to a new commit (via `commit`, `merge`, `pull`, etc.) moves the branch pointer forward, and `HEAD` follows along automatically because it's just referencing the branch.
- A **detached HEAD** happens when `HEAD` points directly at a specific commit instead of at a branch — typically from `git checkout <commit-hash>` or `git checkout <tag>`.
- In detached HEAD state, you can look around, build, test, and even make new commits — but those new commits belong to **no branch**. Nothing is tracking them.
- If you switch to a different branch while in detached HEAD with unsaved commits, those commits can become **unreachable** (effectively orphaned) unless something still points to them.
- The fix, if you want to keep work made in detached HEAD, is simple: `git switch -c <new-branch-name>` *before* switching away, which creates a real branch pointing at your current commit and reattaches HEAD properly.
- If you already switched away and think you lost commits, they're very likely still recoverable — see [[Recovering Lost Commits with the Reflog]].

---

## ⚙️ How It Works

Picture `HEAD` as a sticky note that says "you are here." Normally that sticky note is stuck to a branch label (like `main`), and the branch label itself is stuck to a specific commit. When you commit, the branch label slides forward to the new commit, and since the sticky note is attached to the *label*, it slides along too, automatically, without you doing anything special.

`git checkout <commit-hash>` peels the sticky note off the branch label and sticks it directly onto a commit instead. You're now looking at that exact commit's code — perfectly normal, perfectly safe for reading, running, or testing. But nothing is holding that sticky note's position stable anymore in the way a branch label would. If you commit while detached, you create a new commit, and the sticky note moves to follow it — but there is no branch label following along behind it. The moment you check out something else (a branch, another commit), the sticky note leaves, and if nothing else points to that new commit, it becomes very hard to find again — it's still in Git's database, just with nothing bookmarking it, until garbage collection eventually cleans it up.

`git switch -c <name>` mid-detached-HEAD is the fix: it plants a brand-new branch label right at your current commit and re-attaches the sticky note to *that label* instead of the bare commit — from that point on, it behaves exactly like a normal branch again.

---

## 💻 Examples

```bash
# --- Normal state: HEAD points to a branch ---

git branch --show-current
# main

git symbolic-ref HEAD
# refs/heads/main    <- HEAD points to the branch "main", which points to a commit

# --- Entering detached HEAD on purpose, e.g. to inspect an old commit ---

git log --oneline
# a1b2c3d (HEAD -> main) Latest work
# e4f5g6h Older commit
# h7i8j9k Even older commit

git checkout h7i8j9k
# Note: switching to 'h7i8j9k'.
# You are in 'detached HEAD' state...
# (Git's warning explains this exact situation)

git branch --show-current
# (empty — you're not on a branch anymore)

# --- Looking around safely — this part is completely fine ---

cat some_old_file.js
git log --oneline          # still works, shows history from this point backward

# --- The risky part: committing while detached ---

echo "quick experiment" >> notes.txt
git add notes.txt
git commit -m "Experimenting from an old commit"
# This commit exists! But no branch points to it yet.

# --- SAFE: turning it into a real branch before leaving ---

git switch -c experiment/old-approach
# Now a real branch points at your new commit — HEAD is reattached,
# nothing will be lost when you switch away

# --- RISKY: switching away without creating a branch first ---

git switch main
# Warning: you are leaving 1 commit behind, not connected to
# any of your branches...
# That commit is now unreachable from any branch — but NOT necessarily
# gone. See "Recovering Lost Commits with the Reflog" if this happens.

# --- Checking out a tag also detaches HEAD, by design ---

git checkout v1.2.0
# Tags point at a specific commit, not a moving branch, so checking
# one out is inherently a detached-HEAD operation
```

---

## 🚀 Real World Applications

- **Inspecting a past release** — checking out a tag like `v1.2.0` to look at exactly what shipped in that version naturally puts you in detached HEAD, which is completely expected and fine for read-only inspection.
- **`git bisect`** — Git's binary-search bug-hunting tool intentionally checks out a series of commits directly (detached HEAD) as it narrows down which commit introduced a regression.
- **Building/testing a specific commit in CI** — automated systems frequently check out an exact commit hash rather than a branch, which is inherently a detached-HEAD operation and totally fine since nothing is meant to be committed there.
- **"Oh, I want to keep exploring from here"** — after poking around an old commit and deciding you want to build on it, `git switch -c <name>` turns a casual look-around into a proper, safe branch.

---

## ⚖️ Advantages

- Lets you freely explore any point in history — old commits, tags, any specific snapshot — without needing to create a throwaway branch just to look around.
- Makes tools like `git bisect` possible, efficiently checking out arbitrary commits without cluttering your branch list.
- The "you are in detached HEAD state" warning message itself is genuinely helpful — Git tells you exactly what's going on and how to fix it, right there in the output.

---

## ⚠️ Limitations

- Commits made in detached HEAD are only one branch-switch away from becoming unreachable (though not immediately or permanently gone).
- The concept itself is a common source of beginner confusion — the warning message, while informative, can be intimidating without this background.
- Unreachable commits aren't deleted instantly; they linger until Git's garbage collection eventually cleans them up, which can create a false sense of either total safety or total loss depending on how someone interprets it.

---

## 🚨 Common Mistakes

- Making real work — several commits' worth — while in detached HEAD, then switching branches without creating a branch first, and only then realizing those commits aren't attached to anything.
- Panicking and assuming lost detached-HEAD commits are gone forever — in most cases, `git reflog` still has a record of them (see [[Recovering Lost Commits with the Reflog]]).
- Not noticing the "detached HEAD" warning message at all because it looks like routine command output.
- Confusing detached HEAD with a bug or broken repository state — it's a completely normal, intentional Git feature, not an error condition.
- Forgetting that checking out a tag always results in detached HEAD, and being surprised when `git branch --show-current` comes back empty afterward.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 3: Git Branching (covers HEAD's role as a pointer in depth)
- `git help checkout` (the DETACHED HEAD section specifically)

---

## 💡 Wisdom from Mímir

The single habit that makes detached HEAD stop being scary: the instant you make a commit you actually want to keep while detached, run `git switch -c <name>` immediately, before doing anything else. Don't finish "just one more thing" first. That one-line habit is the entire difference between detached HEAD being a completely ordinary tool for looking around, and it being the setup for a "where did my work go" moment.

---

## 🔗 Related Notes

- [[Recovering Lost Commits with the Reflog]] — the safety net if you already switched away and lost track of a detached-HEAD commit
- [[Understanding the Three Trees]] — background on commits and pointers that this note builds on
- [[Branching with git branch and git switch]] — the normal, attached-HEAD branching workflow this note contrasts against
- [[Undoing Changes - checkout, restore, and reset]] — `checkout`'s dual role (branches vs files) is part of why detached HEAD confuses people coming from older Git habits
