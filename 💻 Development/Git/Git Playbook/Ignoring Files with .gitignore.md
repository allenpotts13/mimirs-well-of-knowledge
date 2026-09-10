---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - git-fundamentals
  - gitignore
  - workflow-hygiene
aliases:
  - .gitignore
  - gitignore
  - Excluding Files from Git
publish: true
---

# <span class="rune">ᛟ</span> Ignoring Files with .gitignore

> *A `.gitignore` file tells Git which untracked files to stop mentioning — it's a filter on `git status`'s attention, not a lock on files Git already knows about.*

---

## 🎯 Purpose

Every project accumulates files that don't belong in version control: build output, dependency folders, editor settings, compiled binaries, secrets, log files. Without `.gitignore`, every `git status` would be a wall of noise, and it would be far too easy to accidentally `git add .` a `node_modules` folder or an `.env` file full of API keys.

`.gitignore` exists to keep the signal-to-noise ratio of your repository high — so that `git status` only ever shows you things you actually might want to commit.

---

## 🧠 Key Ideas

- `.gitignore` is a plain text file, one pattern per line, usually placed at the root of the repository.
- Patterns are matched using a glob-like syntax: `*` matches anything within a path segment, `**` matches across directories, and a trailing `/` restricts a pattern to directories only.
- `.gitignore` **only affects untracked files** — it has zero effect on a file Git is already tracking. This is the single most common source of confusion.
- `!` at the start of a line negates a pattern — it un-ignores something that a broader pattern would otherwise exclude.
- Beyond the per-project `.gitignore`, Git supports a **global gitignore** for personal, machine-specific junk (like `.DS_Store` or `.vscode/`) that shouldn't be forced on every collaborator.
- Lines starting with `#` are comments; blank lines are ignored for readability.

---

## ⚙️ How It Works

Git tracks files in one of three states relevant here: tracked (already committed or staged at some point), untracked, and ignored. `.gitignore` patterns are consulted only when Git is deciding whether to *notice* an untracked file in commands like `git status`, `git add .`, or `git add -A`. If a file matches a pattern, Git pretends it doesn't exist for those purposes.

Think of `.gitignore` as a "do not disturb" list for your working directory, not a shredder. If a file is already on Git's radar — meaning it was committed at least once — adding it to `.gitignore` afterward does nothing. Git already has a copy in its history and will keep tracking future changes to it. This trips up nearly everyone at least once: someone commits a `.env` file by accident, adds `.env` to `.gitignore` in a panic, and is baffled when `git status` still shows it as modified. The fix is to explicitly tell Git to stop tracking it with `git rm --cached`, *then* let `.gitignore` take over from there.

Patterns are also hierarchical — a `.gitignore` file can live in any subdirectory, and its rules apply to that directory and everything below it, layered on top of any `.gitignore` files higher up the tree.

---

## 💻 Examples

```gitignore
# .gitignore — project root

# Ignore all log files anywhere in the project
*.log

# Ignore an entire directory (trailing slash = directory only)
node_modules/
dist/
build/

# Ignore a specific file
.env

# Ignore all files in a folder except one — negation
logs/*
!logs/keep-this.log

# Ignore OS/editor cruft that snuck into this specific repo
.DS_Store
Thumbs.db
```

```bash
# The classic mistake: a file is already tracked, so .gitignore won't hide it
echo "config.local.json" >> .gitignore
git status
# config.local.json still shows up as modified — .gitignore has no power here

# Fix: stop tracking it (removes from repo's future commits, keeps it on disk)
git rm --cached config.local.json
git commit -m "Stop tracking config.local.json"
# NOW .gitignore's rule takes effect on future git status/add commands

# Set up a global gitignore for personal/OS-level junk
git config --global core.excludesfile ~/.gitignore_global

# Then populate it once, for every repo on this machine
echo ".DS_Store" >> ~/.gitignore_global
echo ".vscode/" >> ~/.gitignore_global
echo "*.swp" >> ~/.gitignore_global
# These never need to be committed to any project's own .gitignore
```

---

## 🚀 Real World Applications

- **Node.js projects**: ignoring `node_modules/` so a multi-hundred-megabyte dependency tree never enters version control — it's reproducible from `package.json` instead.
- **Build artifacts**: ignoring `dist/`, `build/`, or `*.class`/`*.pyc` so compiled output never gets committed alongside source, avoiding merge conflicts in generated files.
- **Secrets and local config**: ignoring `.env`, `secrets.yml`, or `*.local` files so credentials never leave a developer's machine.
- **Personal editor settings**: using the global gitignore for `.idea/`, `.vscode/`, or `.DS_Store` so your tooling preferences don't leak into every teammate's repo.
- **Language/framework starter templates**: nearly every `create-react-app`, `django-admin startproject`, or `cargo new` scaffolds a sensible default `.gitignore` for that ecosystem — GitHub's own `gitignore` template repo is the standard reference.

---

## ⚖️ Advantages

- Keeps `git status` and `git add .` clean and trustworthy, so nothing gets committed by accident.
- Centralizes ignore rules in one reviewable file that ships with the project, so every collaborator gets the same behavior.
- The global gitignore keeps personal/OS-specific noise out of shared project files entirely — no team ever has to argue about someone's `.DS_Store`.

---

## ⚠️ Limitations

- Has no effect at all on already-tracked files — a common trap for anyone who assumes it works like a `.env`-style secret manager.
- Doesn't remove anything from Git's *history* — a secret committed once and later ignored is still sitting in old commits and needs history rewriting (or rotation of the secret) to truly remove.
- Patterns can be subtly wrong (e.g., forgetting the trailing `/` on a directory, or a pattern that's too broad and accidentally ignores something needed) — worth testing with `git status` after edits.
- Negation patterns (`!`) can't re-include a file if one of its parent directories was ignored — Git won't look inside an ignored directory at all.

---

## 🚨 Common Mistakes

- Adding a file to `.gitignore` and expecting it to stop being tracked — without `git rm --cached`, it keeps showing up as modified.
- Committing a secret, then "fixing" it only by adding it to `.gitignore` — the secret is still in history and should be treated as compromised.
- Putting personal editor/OS files (`.vscode/`, `.DS_Store`) into the project's shared `.gitignore` instead of a global one, cluttering the file with things unrelated to the project itself.
- Forgetting the trailing slash on directory patterns, which can cause a pattern to also match a file with the same name.
- Writing an ignore pattern *after* the file was already staged in the same session and assuming it retroactively applies — always double-check with `git status`.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 2.2: Recording Changes to the Repository ("Ignoring Files" section)
- `git help gitignore` (or `man gitignore`) — the full, authoritative pattern syntax
- GitHub's `gitignore` template repository — community-maintained starter files per language/framework

---

## 💡 Wisdom from Mímir

The question I ask every time someone says ".gitignore isn't working" is: "was this file ever committed before?" It's almost always yes. `.gitignore` is a filter on the *unknown*, not an eraser for the *known* — Git has a memory, and telling it to ignore something going forward doesn't unwrite what it already remembers. Once that distinction is clear, the confusion evaporates for good.

---

## 🔗 Related Notes

- [[Understanding the Three Trees]] — .gitignore only governs what enters the working-directory-to-staging-area transition in the first place
- [[Undoing Changes - checkout, restore, and reset]] — related but different: removing tracked-but-unwanted content from history
- [[Recovering Lost Commits with the Reflog]] — relevant if you accidentally commit something and need to understand what's recoverable vs. permanently in history
