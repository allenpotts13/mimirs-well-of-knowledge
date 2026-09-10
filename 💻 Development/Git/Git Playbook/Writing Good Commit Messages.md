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
  - conventions
  - team-workflow
aliases:
  - Commit Message Conventions
  - Conventional Commits
  - The 50/72 Rule
publish: true
permalink: git/writing-good-commit-messages
---

# <span class="rune">ᛟ</span> Writing Good Commit Messages

> *A commit message is a letter to the next person who has to understand why this change happened — and that person is usually you, six months from now, with no memory of today.*

---

## 🎯 Purpose

Git will happily accept `git commit -m "stuff"` or `git commit -m "fix"` forever. It never enforces message quality. But `git log`, `git blame`, `git bisect`, and every code archaeologist who ever runs them depend entirely on humans having written something useful. Good commit message conventions exist to make history *searchable and explainable*, not just technically recorded.

This note is about the difference between a commit message that logs "what changed" (which the diff already shows) and one that explains "why," which is the part the diff can never tell you.

---

## 🧠 Key Ideas

- **Imperative mood**: write "Add login validation," not "Added login validation" or "Adds login validation." Think of it as completing the sentence "If applied, this commit will ___."
- **The 50/72 rule**: keep the summary line to ~50 characters, leave a blank line, then wrap the body at ~72 characters per line — this formatting isn't arbitrary, it's what makes `git log` and terminal-width tools display cleanly.
- The summary line answers *what*; the body (when there is one) answers *why* and *how*, plus anything a reviewer needs but the diff won't show.
- **Conventional Commits** is one popular standardized format — `feat:`, `fix:`, `chore:`, `refactor:`, `docs:`, `test:` prefixes — that make history machine-parseable (for changelogs, semantic versioning tools, etc.).
- Message quality compounds: a sloppy message costs seconds now but potentially hours later, multiplied across every person who ever runs `git blame` on that line.

---

## ⚙️ How It Works

A well-formed commit message looks like an email: a subject line, then a body separated by a blank line.

```
Add retry logic to payment webhook handler

Stripe occasionally delivers webhooks out of order or with a brief
delay during high load, which was causing us to mark orders as
failed even though payment succeeded downstream.

Retries with exponential backoff up to 3 attempts before falling
back to the dead-letter queue for manual review.
```

The subject line ("Add retry logic...") is short enough to show up whole in `git log --oneline`, a GitHub PR list, or a terminal 80 columns wide. The body exists because the *code* already shows *what* changed — three retries, exponential backoff — but only the message can explain *why the code needed to change at all* (webhooks arriving out of order). Six months later, when someone is debugging a related webhook issue and runs `git blame`, that "why" is the entire reason the archaeology trip was worth taking.

Conventional Commits formalizes the subject line further: `feat(payments): add retry logic to webhook handler`. The type (`feat`) and optional scope (`payments`) let tooling automatically generate changelogs or bump version numbers (`fix:` → patch, `feat:` → minor, `feat!:` or a `BREAKING CHANGE:` footer → major) without a human curating a release note by hand.

---

## 💻 Examples

```bash
# Bad: past tense, vague, tells you nothing the diff didn't already
git commit -m "fixed bug"

# Good: imperative mood, specific
git commit -m "Fix null pointer when user has no billing address"

# Good: full 50/72-style message via editor (no -m)
git commit
# Summary line (~50 chars):
#   Refactor auth middleware to support multiple providers
#
# Body (wrapped ~72 chars):
#   Previously assumed every request used our own JWT format.
#   This extracts a Strategy interface so OAuth and SAML can
#   plug in without touching the core middleware.
#
#   Closes #482

# Conventional Commits style — useful with tools like semantic-release
# or commitlint that parse the prefix
git commit -m "feat(auth): add OAuth login support"
git commit -m "fix(cart): prevent duplicate line items on rapid clicks"
git commit -m "docs(readme): clarify local setup steps"
git commit -m "chore(deps): bump lodash to 4.17.21"

# Marking a breaking change in Conventional Commits
git commit -m "feat(api)!: remove deprecated /v1/users endpoint"

# Checking how your messages actually read in context
git log --oneline -10
```

---

## 🚀 Real World Applications

- **Changelogs**: tools like `semantic-release` or `standard-version` parse Conventional Commits to auto-generate `CHANGELOG.md` and bump version numbers on release.
- **Debugging with `git bisect`**: a clear message on each candidate commit tells you at a glance what you're testing, instead of having to open every diff.
- **Code review**: a well-written body gives reviewers context before they even open the diff, especially for non-obvious tradeoffs.
- **Onboarding**: new team members reading `git log` on unfamiliar code learn the *reasoning* behind past decisions, not just the mechanics.
- **Incident response**: `git log --grep="payment"` or `git blame` during an outage is only useful if past messages actually describe the behavior, not just "update code."

---

## ⚖️ Advantages

- Imperative mood keeps messages short and consistent, and matches Git's own auto-generated messages (e.g., "Merge branch...").
- The 50/72 rule keeps history readable in every tool, from GitHub's UI to a bare terminal.
- Conventional Commits enables automation (changelogs, semantic versioning, CI gating) with zero extra human effort per commit.
- A good body is the cheapest form of documentation a project ever gets — it's written at the moment of maximum context.

---

## ⚠️ Limitations

- None of this is enforced by Git itself — it requires team discipline, or tooling like `commitlint` / pre-commit hooks to enforce.
- Conventional Commits adds a small amount of ceremony that can feel unnecessary on tiny personal projects.
- Overly terse adherence to "50 characters" can tempt people to omit context that would've fit in a slightly longer line — the rule is a guideline, not a hard ceiling worth fighting for.
- A great message can't rescue a bad commit — if the diff mixes five unrelated changes, no message will make `git blame` useful.

---

## 🚨 Common Mistakes

- Writing messages that just restate the filename or diff ("update app.js") instead of explaining intent.
- Mixing tenses inconsistently across a project's history ("Added," "Fixing," "Add") instead of settling on imperative mood.
- Skipping the body entirely on non-trivial changes, leaving future readers to reverse-engineer "why" from the diff alone.
- Adopting Conventional Commits prefixes without ever using the tooling that makes them worthwhile — paying the ceremony cost without the automation payoff.
- Writing the message as a diary entry ("finally got this working!!") instead of a description useful to someone else.

---

## 📖 Further Reading

- Chris Beams, *"How to Write a Git Commit Message"* — the widely-cited seven rules, including the 50/72 convention
- conventionalcommits.org — the formal Conventional Commits specification
- *Pro Git* (free online book) — Chapter 2, "Committing Your Changes"

---

## 💡 Wisdom from Mímir

I judge a commit message by one test: if I deleted the diff and kept only the message, would a competent engineer still understand what changed and why it mattered? "Fix bug" fails that test instantly. "Fix null pointer when user has no billing address, caused by assuming address is always present after checkout" passes it — you don't even need to open the diff to know exactly what to look for. Write for that reader, not for the five seconds it takes to type `-m "wip"`.

---

## 🔗 Related Notes

- [[Committing Changes with git commit]] — the mechanics this note's conventions apply to
- [[Understanding the Three Trees]] — what's actually being captured when you write this message
- [[Squashing Commits]] — cleaning up a messy sequence of "wip" commits into one well-described one
- [[Viewing History with git log]] — where these messages ultimately get read
