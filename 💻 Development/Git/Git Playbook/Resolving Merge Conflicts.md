---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - merging
  - conflict-resolution
  - troubleshooting
aliases:
  - Merge Conflicts
  - Conflict Markers
  - git merge --abort
---

# 📚 Resolving Merge Conflicts

> *A merge conflict isn't Git failing — it's Git honestly admitting that two people changed the same lines and it refuses to guess which version you actually want.*

---

## 🎯 Purpose

Automatic merging works great right up until both sides touch the exact same lines of the exact same file. At that point Git has two equally plausible versions of the truth and no way to pick a winner — so it stops, marks the disputed lines directly in the file, and hands the decision to you. Knowing how to read those markers, track your progress, and — critically — bail out cleanly if it's the wrong moment, turns a stressful situation into a routine one.

---

## 🧠 Key Ideas

- Conflicts are marked directly inside the affected file using `<<<<<<<`, `=======`, and `>>>>>>>` — Git doesn't hide them anywhere else.
- `<<<<<<< HEAD` through `=======` shows *your* current branch's version; `=======` through `>>>>>>> branch-name` shows the *incoming* branch's version.
- `git status` during a conflicted merge lists exactly which files still have unresolved conflicts ("both modified") versus which are already resolved and staged.
- Resolving a conflict means editing the file to the correct final content, deleting all three marker lines, then `git add`-ing that file to mark it resolved.
- `git merge --abort` cleanly cancels the entire merge and restores everything to exactly how it was before you ran `git merge` — the safest way out if things go sideways.

---

## ⚙️ How It Works

When you run `git merge` and a conflict occurs, Git pauses mid-operation. It successfully auto-merges every file it can, and for the ones it can't, it writes *both* versions into the file itself, wrapped in markers, so you have full context for the decision:

```
<<<<<<< HEAD
const MAX_RETRIES = 3;
=======
const MAX_RETRIES = 5;
>>>>>>> feature-retry-tuning
```

Everything between `<<<<<<< HEAD` and `=======` is what's currently on your branch. Everything between `=======` and `>>>>>>> feature-retry-tuning` is what's coming in from the branch you're merging. Your job is to decide what the file should actually say — keep one side, keep the other, blend them into something new entirely — and then delete all three marker lines so the file is valid code/text again.

At this point Git considers the *repository itself* to be in a special "mid-merge" state — it remembers you started a merge (via `MERGE_HEAD`) and won't let you do much else until you finish it. `git status` becomes your map here: it explicitly separates files still needing attention from ones you've already fixed and staged, so you never have to guess what's left.

Sometimes, mid-conflict, you realize this isn't the moment — maybe you picked the wrong branch, maybe you need to pull a newer `main` first, maybe there are simply too many conflicts to deal with right now. `git merge --abort` unwinds the entire attempt: files return to their pre-merge state, and it's as if you never ran `merge` at all. This is the "undo" button for a conflict you're not ready to face.

---

## 💻 Examples

```bash
# Start a merge that results in a conflict
git switch main
git merge feature-retry-tuning
# Auto-merging config.js
# CONFLICT (content): Merge conflict in config.js
# Automatic merge failed; fix conflicts and then commit the result.

# See exactly which files still need resolving
git status
# both modified:   config.js
# (files with no conflict are already staged automatically)

# Open the conflicted file and look for the markers
# <<<<<<< HEAD
# const MAX_RETRIES = 3;
# =======
# const MAX_RETRIES = 5;
# >>>>>>> feature-retry-tuning

# Decide on the correct value, remove ALL marker lines, save the file
# (Suppose we decide 5 is right — delete lines 1, 3, 5 above, keep line 4)

# Tell Git this file's conflict is resolved
git add config.js

# Check status again — should now say "all conflicts fixed"
git status

# Once every conflicted file is resolved and staged, finish the merge
git commit
# Opens the pre-filled merge commit message — edit or accept as-is

# Changed your mind partway through? Cleanly cancel the whole merge
git merge --abort
# Working directory and staging area return to exactly how they
# looked before "git merge" was run — nothing is left half-done

# See a nicer diff of the conflict (shows the common ancestor too)
git diff --diff-filter=U   # lists only files with unresolved conflicts
git checkout --conflict=diff3 config.js   # adds the merge-base section too
```

---

## 🚀 Real World Applications

- **PR merges with drift**: resolving conflicts when your feature branch and `main` both touched the same config or import block while you were working in parallel.
- **Merging long-lived branches**: release branches or long-running feature branches accumulate more potential conflict surface the longer they diverge.
- **Reverting mid-merge**: `--abort` when you realize you should `git pull --rebase` first, or that you merged the wrong branch entirely.
- **Pair debugging a conflict**: using `git status` to divide up a large multi-file conflict between two people, each taking a subset of the listed files.
- **Merge tools**: configuring a visual diff/merge tool (`git mergetool`) that reads the same three-way markers but presents them side-by-side instead of inline.

---

## ⚖️ Advantages

- Conflict markers live directly in the file with full context — you're never guessing what either side intended.
- `git status` gives an authoritative, always-current checklist of exactly what's left to resolve.
- `--abort` makes conflict resolution low-risk — if it goes badly, you can always return to the exact starting point.
- The mid-merge state persists across sessions — you can walk away and come back later without losing progress (as long as you don't run other Git commands that assume a clean state).

---

## ⚠️ Limitations

- Git can only flag *textual* conflicts (same lines touched) — it has no idea if two non-overlapping changes are *logically* incompatible (e.g., one side renames a function, the other calls it by the old name elsewhere).
- Resolving a conflict incorrectly is entirely possible and Git won't catch it — tests and careful review still matter after resolution.
- Large, long-diverged branches can produce dozens of conflicting files at once, which is genuinely tedious regardless of tooling.
- `--abort` only works while the merge is still in progress — once you've committed the resolution, you need `revert` or `reset` instead to undo it.

---

## 🚨 Common Mistakes

- Committing a file with leftover `<<<<<<<`/`=======`/`>>>>>>>` markers still in it because they weren't fully deleted — this breaks the file (and often the build) silently.
- Resolving conflicts but forgetting to `git add` the file afterward, so `git commit` still refuses to proceed.
- Panicking and closing the terminal or deleting the repo instead of just running `git merge --abort`.
- Blindly picking "my version" or "their version" for every conflict without actually reading both sides — sometimes the correct resolution is a blend of both.
- Running unrelated Git commands (like starting another `git pull`) while a merge is still unresolved, compounding the confusion.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 3, "Basic Merge Conflicts"
- `git help merge` (see the "HOW CONFLICTS ARE PRESENTED" section)
- `git help checkout` — the `--conflict=diff3` option for showing the common ancestor

---

## 💡 Wisdom from Mímir

The moment a conflict appears, resist the urge to fix it fast. Read *both* sides of the marker first, and ask what each branch's author was actually trying to accomplish — the correct resolution is often neither version verbatim, but a synthesis of both intents. And if you're ever unsure whether you're making things worse, remember `--abort` costs you nothing: it's always safer to cancel and start the merge fresh with a clear head than to push through a resolution you don't trust.

---

## 🔗 Related Notes

- [[Merging Branches]] — how a merge reaches the conflicted state in the first place
- [[Inspecting Changes with git diff]] — reading diffs more generally, useful for double-checking a resolution
- [[Rebasing vs Merging]] — conflicts can also occur during rebase, with a different resolution flow
- [[Recovering Lost Commits with the Reflog]] — a safety net if a conflict resolution goes badly wrong after committing
