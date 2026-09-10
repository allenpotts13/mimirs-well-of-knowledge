---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - remotes
  - collaboration
  - fetch
  - pull
  - push
aliases:
  - "Working with Remotes: fetch, pull, and push"
  - git fetch
  - git pull
  - git push
  - Upstream Tracking
publish: true
permalink: git/working-with-remotes-fetch-pull-and-push
---

# <span class="rune">ᛟ</span> Working with Remotes: fetch, pull, and push

> *`git fetch` safely downloads what's new without touching anything of yours; `git pull` does that same download and then immediately merges (or rebases) it into your current branch — conflating the two is the single most common source of "why did my branch just change" surprise.*

---

## 🎯 Purpose

Git is distributed — your local repository is a complete copy of history, and a "remote" (usually `origin`) is just another copy, typically hosted somewhere like GitHub. Working with a team means regularly syncing those two copies in both directions: pulling down others' work, pushing up your own.

The confusion beginners run into almost always comes from `git pull` doing *two* things at once (download, then merge) under one name, while `git fetch` — which only does the safe first half — gets underused simply because people don't realize it's an option. This note untangles the three core remote commands so you always know exactly what's about to happen to your branch before you run something.

---

## 🧠 Key Ideas

- `git fetch` downloads new commits, branches, and tags from the remote into your local copy of the remote's branches (e.g. `origin/main`) — **it never touches your working directory or your current branch.** Always safe to run.
- `git pull` = `git fetch` + a merge (by default) of the fetched remote branch into your current branch. This **does** change your current branch and can trigger a merge commit or conflicts.
- `git pull --rebase` swaps the merge step for a rebase instead — replays your local commits on top of the fetched history rather than creating a merge commit. See [[Rebasing vs Merging]] for the tradeoffs.
- `git push` uploads your local commits on the current branch to the remote, updating the remote branch to match — assuming the remote branch hasn't diverged (i.e., your local history is a fast-forward of it).
- `git push -u origin <branch>` (or `--set-upstream`) both pushes the branch and establishes a tracking relationship, so that afterward plain `git push` / `git pull` know which remote branch to sync with automatically.
- A remote-tracking branch like `origin/main` is a local bookmark of where the remote's `main` was as of your last `fetch` — it only updates when you fetch (or pull), not in real time.

---

## ⚙️ How It Works

Think of `origin/main` as a snapshot Git keeps of "what `main` looked like on the remote, last time I checked." `git fetch` is purely "go check, and update that snapshot" — it downloads any commits the remote has that you don't, and moves `origin/main` to point at the latest one. Nothing about *your* `main` branch or your working directory changes; you now just have more information available locally, which you can inspect with `git log origin/main` or `git diff main origin/main` before deciding what to do with it.

`git pull` is that same fetch, immediately followed by integrating those new commits into your current branch — by default via a merge (creating a merge commit if your branch has also moved forward independently), or via rebase if you pass `--rebase` (replaying your commits on top instead, for a linear history).

`git push` works in the opposite direction: it asks the remote to fast-forward its branch to include your new local commits. If the remote branch has commits you don't have locally (because someone else pushed in the meantime), the push is rejected — Git refuses to silently overwrite work it hasn't seen, and expects you to `fetch`/`pull` and reconcile first (see [[Force-Pushing Safely with --force-with-lease]] for the rare, deliberate exception).

The `-u`/`--set-upstream` flag on push is a one-time bookkeeping step: it tells Git "this local branch corresponds to that remote branch," so future plain `git push` and `git pull` calls don't need you to spell out the remote and branch name every time.

---

## 💻 Examples

```bash
# --- Safe, non-destructive check-in on what's new remotely ---

git fetch origin
# Downloads new commits/branches/tags. Your working directory and
# current branch are completely untouched.

git log main..origin/main --oneline
# See exactly what's new on the remote's main that you don't have yet

git diff main origin/main
# See the actual content differences before deciding to merge

# --- Pulling: fetch + merge in one step ---

git pull
# Equivalent to: git fetch  +  git merge origin/<current-branch>
# This DOES change your current branch — may create a merge commit
# or produce conflicts to resolve

# --- Pulling with rebase instead of merge ---

git pull --rebase
# Equivalent to: git fetch  +  git rebase origin/<current-branch>
# Replays your local commits on top of the fetched ones — keeps
# history linear instead of adding a merge commit

# --- First push of a new local branch ---

git switch -c feature/checkout
# ... make commits ...
git push -u origin feature/checkout
# Pushes the branch AND sets up tracking — from now on, plain
# `git push` and `git pull` on this branch know where to sync

# --- Subsequent pushes, once tracking is set up ---

git push
# No need to specify origin/branch again — the -u step remembered it

# --- Seeing what remotes are configured ---

git remote -v
# origin  https://github.com/you/repo.git (fetch)
# origin  https://github.com/you/repo.git (push)
```

---

## 🚀 Real World Applications

- **Starting the day** — `git fetch` first, then reviewing `git log main..origin/main` before deciding to pull, is a safer habit than blindly `git pull`-ing into whatever state your branch is in.
- **Setting up a new feature branch** — `git push -u origin <branch>` on the first push makes every future push/pull on that branch a plain, no-argument command.
- **Keeping a linear history on a team that dislikes merge commits** — `git pull --rebase` (often set as the default via `git config pull.rebase true`) keeps local work rebased on top of the latest remote instead of interleaving merge commits.
- **Checking a teammate's pushed branch without merging anything** — `git fetch` then `git switch --detach origin/their-branch` (or checking out a local branch from it) lets you look around safely.
- **CI/CD pipelines** — automated systems typically `fetch` (or a shallow clone-equivalent) rather than `pull`, since they never have local commits that need merging.

---

## ⚖️ Advantages

- `fetch` gives you full visibility into what's changed remotely before it touches anything of yours — a genuinely safe operation to run anytime, as often as you like.
- `push -u` removes repetitive typing for the rest of a branch's life once set up.
- `pull --rebase` keeps history clean and linear for teams that prefer that style over frequent merge commits.

---

## ⚠️ Limitations

- `git pull`'s default merge behavior can create a lot of small, low-information merge commits on an actively-developed branch if you pull frequently.
- `pull --rebase` rewrites your local commits' hashes (since rebase replays them), which is fine locally but the same caution about shared history from [[Undoing Changes - checkout, restore, and reset]] and [[Reverting a Commit vs Resetting]] applies if those commits were already pushed elsewhere.
- `push` will simply refuse if the remote has diverged — it doesn't automatically merge or rebase for you, which is a feature, but can be a rough edge for beginners who don't know what to do next.
- Remote-tracking branches (`origin/main`) are only ever as fresh as your last `fetch`/`pull` — they don't update live.

---

## 🚨 Common Mistakes

- Running `git pull` reflexively without realizing it can immediately create a merge commit or a conflict — `git fetch` first is often the more deliberate choice.
- Forgetting `-u` on a brand-new branch's first push, then being confused why plain `git push` afterward asks for more information or fails.
- Assuming `git fetch` updates your working files — it doesn't touch them at all, which surprises people expecting it to behave like `pull`.
- Getting a rejected push, and reaching for `--force` instead of pulling/rebasing first to understand what diverged (see [[Force-Pushing Safely with --force-with-lease]] for when force is actually appropriate).
- Not realizing `origin/main` in `git log` or `git diff` refers to a possibly-stale local snapshot, not necessarily the remote's current live state, until you `fetch` again.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 2.5: Git Basics - Working with Remotes
- `git help fetch`, `git help pull`, `git help push`

---

## 💡 Wisdom from Mímir

I default to `git fetch` and looking before I `pull` on anything that matters — a shared release branch, `main`, anything with people actively pushing to it. It costs three extra seconds and it means I always know what I'm about to merge into my work before it happens, rather than finding out after the fact when a merge commit or a conflict shows up uninvited.

---

## 🔗 Related Notes

- [[Force-Pushing Safely with --force-with-lease]] — what to do when a plain push is rejected and you're certain an overwrite is correct
- [[Rebasing vs Merging]] — the deeper tradeoffs behind `pull`'s merge-vs-rebase choice
- [[Understanding HEAD and Detached HEAD State]] — relevant when checking out a remote branch or commit directly to look around
- [[Common Git Workflows - Feature Branch, Trunk-Based, and Git Flow]] — how fetch/pull/push patterns differ across team workflows
