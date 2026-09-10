---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - git-fundamentals
  - history
  - git-log
  - debugging
aliases:
  - git log
  - Viewing Commit History
---

# 📚 Viewing History with git log

> *`git log` is Git's window into everything that's ever happened in a repository — and knowing its handful of power flags turns it from a wall of text into a precise, queryable timeline.*

---

## 🎯 Purpose

Every commit you've ever made is sitting in your repository, permanently, but by default `git log` shows it to you as a long, linear scroll of full hashes, author lines, dates, and messages — useful for nothing at a glance. Real work needs to answer specific questions: "what changed on this branch since we forked from main?", "what exactly did this commit change?", "who touched this file last month?", "what did we ship between these two dates?"

`git log`'s flags exist to turn the raw commit graph into answers to exactly those questions, without needing a GUI tool.

---

## 🧠 Key Ideas

- Plain `git log` shows full commit hashes, author, date, and message for every commit reachable from `HEAD`, newest first — verbose, and only shows one branch's history unless told otherwise.
- `--oneline` compresses each commit to a single line (short hash + message) — the fastest way to scan a lot of history at once.
- `--graph` draws the branch/merge structure as ASCII art alongside the log, showing where branches diverged and merged.
- `--all` includes every branch and tag, not just the current one — combined with `--oneline --graph`, this is the single most useful "show me the whole picture" command in Git.
- `-p` (or `--patch`) shows the actual diff introduced by each commit, not just its message — turns `log` into a changelog you can read line-by-line.
- Filters like `--author`, `--since`/`--until`, and `-- <path>` narrow the log down to exactly what you're looking for, instead of scrolling through everything.

---

## ⚙️ How It Works

`git log` walks the commit graph backward from a starting point (`HEAD` by default) following parent links, and prints what it finds. Every flag either changes **what's included** in that walk (which commits) or **how much detail** is shown for each one.

Think of it in two categories:

**Scope filters** — decide *which commits* show up at all:
- `--all` — start the walk from every branch/tag, not just the current branch.
- `--author="name"` — only commits whose author matches.
- `--since="2 weeks ago"` / `--until="2026-08-01"` — only commits in a date range (accepts human-readable relative dates, which is one of Git's genuinely pleasant little features).
- `-- <path>` — only commits that touched a specific file or directory. Note the `--` before the path — it disambiguates "this is a path" from "this is a branch/revision name."

**Presentation options** — decide *how much detail* each shown commit gets:
- `--oneline` — one line per commit, hash + subject only.
- `--graph` — adds the ASCII branch/merge topology.
- `-p` — full diff for every commit shown (can be a lot of output; usually paired with a scope filter to keep it manageable).
- `--stat` — a compact summary of files changed and line counts per commit, without full diffs (see [[Inspecting Changes with git diff]] for the same flag on `diff`).

These combine freely — `git log --oneline --graph --all --since="1 week ago"` is a completely normal, useful command.

---

## 💻 Examples

```bash
# --- The classic "show me the whole branch picture" command ---

git log --oneline --graph --all
# * a1b2c3d (HEAD -> main) Merge branch 'feature/login'
# |\
# | * e4f5g6h (feature/login) Add login form validation
# | * h7i8j9k Add login form markup
# |/
# * k1l2m3n Initial commit

# --- Reading the actual diff for recent commits ---

git log -p -3
# Shows the full diff for the last 3 commits, one after another —
# essentially a changelog you can read like a diary

# --- Filtering by author ---

git log --author="Allen"
# Only commits where the author name/email matches "Allen"

git log --oneline --author="curse1313@gmail.com"
# Combine filters with presentation flags freely

# --- Filtering by date range ---

git log --since="2026-08-01" --until="2026-09-01" --oneline
# Everything committed in August 2026

git log --since="2 weeks ago" --oneline
# Git understands relative, human-readable dates too

# --- Scoping to one file's history ---

git log -- src/app.js
# Only commits that touched src/app.js — great for "when did this break?"

git log --oneline -p -- src/app.js
# Same, but with the actual diffs for each of those commits

# --- Combining everything: "what did Allen change in this file last month?" ---

git log --oneline --author="Allen" --since="1 month ago" -- src/app.js
```

---

## 🚀 Real World Applications

- **Onboarding to an unfamiliar repo** — `git log --oneline --graph --all` quickly shows how branches have historically diverged and merged.
- **Debugging a regression** — `git log -p -- path/to/file` lets you read exactly what changed in a file over time, line by line, to spot when a bug was introduced.
- **Writing a release changelog** — `git log --since=<last-release-date> --oneline` gives a quick list of everything that shipped since the last tag.
- **Code review / accountability** — `git log --author="name"` scoped to a file or directory shows exactly what one person has touched recently.
- **Sanity-checking a rebase or merge before pushing** — `--graph --all` shows the real shape of history, catching accidental extra merge commits.

---

## ⚖️ Advantages

- Entirely local and instant — no network call needed to inspect history.
- Extremely composable — filters and presentation flags mix and match for very specific queries.
- `--graph` gives a visual understanding of branch topology without needing a GUI.

---

## ⚠️ Limitations

- Plain `git log` output is verbose and easy to get lost in on a busy repository — the useful flags aren't the defaults.
- `-p` on a large range of commits can produce an overwhelming amount of output; usually needs to be paired with a scope filter (`-n`, `--since`, or `-- path`).
- `--graph` ASCII art can get visually cluttered on repositories with many simultaneous branches.
- Date filters like `--since`/`--until` operate on commit dates, which can differ from author dates after a rebase — occasionally surprising.

---

## 🚨 Common Mistakes

- Running plain `git log` on a large repo and being overwhelmed, instead of reaching for `--oneline` first.
- Forgetting `--all` and wondering why commits on other branches don't show up in the log.
- Omitting the `--` before a path filter and having Git misinterpret the argument, especially when the path could also be mistaken for a branch name.
- Using `-p` on the entire history of a large repo and getting a wall of output instead of scoping it with a range or path first.
- Assuming `--author` matches the *committer* — it actually matches the *author* field, which can differ after a rebase or cherry-pick performed by someone else.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 2.3: Git Basics - Viewing the Commit History
- `git help log` (the full list of pretty-format and filtering options is much larger than covered here)

---

## 💡 Wisdom from Mímir

`git log --oneline --graph --all` is one of those commands worth aliasing early — I've seen it saved as `git lg` in more `.gitconfig` files than I can count. The moment you can see the *shape* of history, not just a scrolling list of messages, branching and merging stop feeling mysterious. Half of "Git is confusing" complaints disappear once someone can actually see what their branches are doing.

---

## 🔗 Related Notes

- [[Understanding the Three Trees]] — background on what a commit actually represents
- [[Inspecting Changes with git diff]] — the complementary tool for seeing *what* changed, as opposed to *when* and *by whom*
- [[Tracing Changes with git blame]] — for pinpointing which commit last touched a specific line, rather than a whole file
- [[Writing Good Commit Messages]] — why the messages you're scanning in `git log` matter so much
