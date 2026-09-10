---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - git-fundamentals
  - undoing-changes
  - working-directory
  - staging-area
  - mental-model
aliases:
  - "Undoing Changes: checkout, restore, and reset"
  - git restore
  - git reset
  - git checkout
  - Unstaging a File
  - Discarding Changes
publish: true
---

# 📚 Undoing Changes: checkout, restore, and reset

> *Undoing something in Git is never one operation — it's always a question of which of the three trees (working directory, staging area, repository) you actually want to rewind, and `restore` and `reset` exist precisely to let you answer that question precisely.*

---

## 🎯 Purpose

For years, `git checkout` was the tool for two completely unrelated jobs: switching branches, and discarding file changes. That overload is exactly why so many people have accidentally nuked work they meant to keep — the command that switches your branch and the command that throws away edits looked identical.

Git 2.23 introduced `git restore` (and `git switch`) specifically to split those responsibilities apart. `restore` now owns "undo changes to files," and `switch` owns "change branches." Meanwhile `git reset` handles a different but related job: moving *which commit your branch pointer is on*, with three levels of blast radius (`--soft`, `--mixed`, `--hard`) depending on how much of the three-trees state you want to drag along with it.

This note exists so you can pick the *right* undo command on the first try, instead of Googling "how do I undo" every single time and copy-pasting whatever StackOverflow answer looks scariest.

---

## 🧠 Key Ideas

- Every undo command in Git is really answering: **which tree(s) am I rewinding — working directory, staging area, repository — and to what?** See [[Understanding the Three Trees]] for the full model.
- `git restore <file>` — discards working-directory changes, replacing the file with the version from the staging area (or `HEAD` if unstaged since). Touches **working directory only**.
- `git restore --staged <file>` — unstages a file, moving it from the staging area back to "just a working-directory change," without touching the file's actual content. Touches **staging area only**.
- `git checkout <file>` is the older, dual-purpose ancestor of `git restore <file>` — it still works, but it's the same command that also switches branches, which is exactly the ambiguity `restore`/`switch` were built to remove.
- `git reset --soft <commit>` — moves the branch pointer (repository) to `<commit>`, but leaves the staging area and working directory untouched. The changes from the "undone" commits reappear as staged changes.
- `git reset --mixed <commit>` (the **default** if you omit a flag) — moves the branch pointer *and* resets the staging area to match, but leaves the working directory alone. The changes reappear as unstaged, working-directory edits.
- `git reset --hard <commit>` — moves the branch pointer, resets staging, **and overwrites the working directory** to match. This is the only one of the three that can permanently discard uncommitted work — use it deliberately, not reflexively.

---

## ⚙️ How It Works

Picture the three trees again: working directory (your files), staging area (the draft of the next commit), repository (permanent history).

**`restore` operates left-to-right, tree by tree, and never touches the repository's commit history:**
- `git restore <file>` copies the staged (or last-committed) version of a file *into* the working directory, overwriting your edits.
- `git restore --staged <file>` copies the last-committed version of a file *into* the staging area, effectively undoing an `add` without touching your actual edits in the working directory.

**`reset` operates on the branch pointer first, then optionally cascades that change forward into staging and the working directory:**

Think of `reset` as choosing how far the shockwave travels:
- `--soft` — shockwave stops at the repository. Only the pointer moves. Everything that *was* in the undone commits is still sitting in your staging area, ready to be re-committed differently.
- `--mixed` — shockwave reaches the staging area too. The undone commits' changes land back as plain unstaged edits.
- `--hard` — shockwave reaches all the way to your working directory. Your files on disk are forcibly rewritten to match the target commit. Anything uncommitted gets steamrolled.

A useful way to remember it: **soft = "just forget the last commit(s), keep everything staged"; mixed = "forget the commit(s) and unstage, but keep my edits visible in files"; hard = "pretend those commits and any uncommitted work never happened."**

---

## 💻 Examples

```bash
# --- restore: undoing working-directory edits ---

echo "oops, broke this" >> config.js
git restore config.js
# config.js is now back to whatever was last staged/committed — edit is gone

# --- restore: unstaging a file you added by mistake ---

git add secrets.env
git restore --staged secrets.env
# secrets.env is unstaged again, but the file itself is untouched on disk
# (you'd still want to add it to .gitignore — see Ignoring Files with .gitignore)

# --- reset --soft: "I want to redo my last commit's message/contents" ---

git reset --soft HEAD~1
# The last commit is undone, but all its changes are now staged and ready
# to be re-committed (e.g. with a better message, or combined with more work)
git commit -m "A better commit message this time"

# --- reset --mixed (default): "undo the commit AND unstage, but keep my edits" ---

git reset HEAD~1
# Same as: git reset --mixed HEAD~1
# The commit is gone, changes are back in the working directory, unstaged

# --- reset --hard: "throw it all away, I don't want any of this" ---

git status                 # always check first — this is destructive
git reset --hard HEAD~1
# Last commit AND any uncommitted changes in the working directory are gone.
# Only run this when you are certain you don't need any of it.

# --- reset --hard to a specific commit, e.g. to abandon everything since ---

git log --oneline          # find the commit hash you want to rewind to
git reset --hard a1b2c3d
```

---

## 🚀 Real World Applications

- **"I staged the wrong files"** — `git restore --staged .` unstages everything without touching your edits, letting you re-add just what belongs together.
- **"I want to rewrite my last commit entirely"** — `git reset --soft HEAD~1` followed by editing files and re-committing is a common way to fix a bad commit before it's pushed.
- **"I pulled in a branch that's a mess and want to start clean from origin"** — `git fetch` then `git reset --hard origin/main` snaps your local branch to match the remote exactly.
- **"I want to discard a bad experiment in one file"** — `git restore path/to/file.js` is the fast, precise way, instead of manually re-typing changes.
- **Combining commits before pushing** — `git reset --soft` back several commits, then making one clean commit, is a lighter-weight alternative to interactive rebase for simple cases.

---

## ⚖️ Advantages

- `restore` gives you two narrowly-scoped, clearly-named commands instead of one overloaded `checkout`, reducing the chance of an accidental branch switch when you meant to discard a file.
- `reset`'s three levels (`--soft`/`--mixed`/`--hard`) let you choose exactly how much you want to undo instead of an all-or-nothing operation.
- Both are fast, local operations — no network needed, and (short of `--hard` wiping uncommitted work) fully within Git's safety net.

---

## ⚠️ Limitations

- `reset --hard` is genuinely destructive to **uncommitted** changes — there's no staging-area or working-directory history to recover them from (though committed work can often be recovered via [[Recovering Lost Commits with the Reflog]]).
- `reset` rewrites *where your branch pointer is* — doing this on a branch you've already pushed and others have pulled creates diverging history. See [[Reverting a Commit vs Resetting]] for the safe alternative in that situation.
- `restore` and `reset` don't warn you with a confirmation prompt the way a file manager's "delete" might — Git trusts you meant it.
- Older tutorials, Stack Overflow answers, and muscle memory still lean on `git checkout -- <file>`, which is functionally close to `git restore <file>` but carries the branch-switching ambiguity `restore` was built to avoid.

---

## 🚨 Common Mistakes

- Running `git reset --hard` without first running `git status` to check what's actually about to be lost.
- Using `git reset --hard` on a branch that's already been pushed and pulled by teammates — this rewrites shared history and causes painful sync problems (use `git revert` instead — see [[Reverting a Commit vs Resetting]]).
- Confusing `git restore <file>` (discard working-directory edits) with `git restore --staged <file>` (unstage, keep edits) — they answer different questions and mixing them up either loses work or leaves it staged when you didn't want it to be.
- Typing `git checkout <file>` out of habit and not realizing that if `<file>` happens to match a branch name, Git's behavior can become ambiguous.
- Forgetting that `--mixed` is the *default* for `git reset` — running plain `git reset HEAD~1` does more than some people expect (it unstages, not just "undoes the commit").

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 7.7: Git Tools - Reset Demystified
- `git help restore`, `git help reset`, `git help checkout`

---

## 💡 Wisdom from Mímir

The safest habit I know for this whole family of commands: before you type `--hard` anything, run `git status` and actually read it. Not skim it — read it. Every "I lost a day of work" story I've heard starts with someone who was *sure* nothing important was uncommitted. Git will tell you exactly what's about to disappear if you just ask it first.

---

## 🔗 Related Notes

- [[Understanding the Three Trees]] — the mental model that makes `restore` vs `reset` make sense in the first place
- [[Reverting a Commit vs Resetting]] — the safe alternative to `reset` once history has been shared with others
- [[Recovering Lost Commits with the Reflog]] — the safety net for when a `reset --hard` (or similar) goes further than intended
- [[Stashing Changes with git stash]] — a non-destructive way to set changes aside instead of discarding them outright
