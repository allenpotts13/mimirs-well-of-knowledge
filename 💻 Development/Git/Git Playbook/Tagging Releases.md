---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Beginner
tags:
  - tags
  - releases
  - versioning
  - git-fundamentals
aliases:
  - git tag
  - Annotated Tags
  - Lightweight Tags
  - Release Tagging
publish: true
---

# 📚 Tagging Releases

> *A tag is a permanent, human-readable bookmark for one specific commit — usually "this exact snapshot is version 1.2.0" — and Git gives you two flavors, a bare pointer or a full annotated record, depending on how much you need it to remember.*

---

## 🎯 Purpose

Commit hashes are precise but meaningless to a human — nobody wants to tell a client "we shipped `a1b2c3d`." Tags exist to give a specific, permanent commit a memorable name, almost always tied to a release version (`v1.0.0`, `v2.3.1-beta`), so that "what code shipped in version 2.3.1" is always an exact, unambiguous, one-command answer.

Unlike branches, tags don't move — once created, a tag points at one commit forever (short of deliberately deleting and recreating it), which is exactly the property you want for marking a release.

---

## 🧠 Key Ideas

- A **lightweight tag** is just a name pointing directly at a commit — no extra metadata, essentially a bookmark. Created with `git tag <name>`.
- An **annotated tag** is a full Git object of its own — it stores a message, the tagger's name and email, and a date, and can be GPG-signed for verification. Created with `git tag -a <name> -m "message"`.
- Annotated tags are generally recommended for anything meant to represent a real release — the extra metadata (who tagged it, when, why) matters for audit trails and release notes.
- Tags, unlike branches, are **static** — they don't move forward as new commits are made. A branch pointer follows new commits; a tag stays fixed on the commit it was created against.
- **The classic gotcha:** `git push` does **not** push tags by default. You need `git push --tags` (all tags) or `git push origin <tagname>` (one specific tag) to actually share a tag with a remote.
- Tags can be checked out directly, which puts you in a [[Understanding HEAD and Detached HEAD State|detached HEAD state]] — completely expected, since a tag points at a fixed commit, not a branch.

---

## ⚙️ How It Works

Under the hood, a lightweight tag is nothing more than a named reference (like a branch) that points at a commit and never moves — Git stores it essentially as a label with no extra content of its own.

An annotated tag is different: Git creates an actual tag *object* in the repository's object database, and that object stores the message, tagger identity, and timestamp — then that tag object points at the commit, one extra layer of indirection compared to a lightweight tag. That extra object is exactly what enables `git show <tag>` to display a full message and metadata, and what makes GPG-signing possible (`git tag -s`) — you're signing a real, persistent object, not just a name.

The push behavior surprises a lot of people, but it follows Git's general philosophy of not sending more than you explicitly asked for: `git push` only pushes commits reachable on the branch(es) you're pushing, and tags are a separate category of ref entirely. You have to explicitly say "also send tags."

---

## 💻 Examples

```bash
# --- Lightweight tag: quick, no metadata ---

git tag v1.0.0-quick
# Points at the current HEAD commit — that's it, no message, no author record

# --- Lightweight tag on a specific (older) commit ---

git log --oneline
git tag v0.9.0-quick e4f5g6h
# Tags a specific past commit, not necessarily the current HEAD

# --- Annotated tag: the recommended way for real releases ---

git tag -a v1.2.0 -m "Release 1.2.0: adds checkout flow, fixes cart bug"
# Creates a full tag object with message, tagger name/email, and date

git show v1.2.0
# tag v1.2.0
# Tagger: Allen <developer@example.com>
# Date:   Wed Sep 2 2026 ...
#
#     Release 1.2.0: adds checkout flow, fixes cart bug
#
# commit a1b2c3d...
# ...(then the commit's own diff/details)

# --- Signed annotated tag (requires GPG configured) ---

git tag -s v1.2.0 -m "Release 1.2.0"
# Same as -a, but cryptographically signed — verifiable with:
git tag -v v1.2.0

# --- Listing tags ---

git tag
# v0.9.0-quick
# v1.0.0-quick
# v1.2.0

git tag -l "v1.2.*"
# Filter tags by pattern

# --- THE GOTCHA: pushing tags ---

git push origin main
# This does NOT push any tags, even ones on commits just pushed!

git push origin v1.2.0
# Pushes just this one tag

git push --tags
# Pushes ALL local tags not yet on the remote

git push --follow-tags
# Pushes commits AND any annotated tags reachable from them —
# a nice middle ground that skips lightweight tags

# --- Checking out a tag (puts you in detached HEAD, expectedly) ---

git checkout v1.2.0
# You are in 'detached HEAD' state — completely normal for inspecting
# an exact released snapshot

# --- Deleting a tag (locally and remotely) ---

git tag -d v1.0.0-quick
git push origin --delete v1.0.0-quick
```

---

## 🚀 Real World Applications

- **Marking release versions** — `v1.0.0`, `v2.3.1`, following [Semantic Versioning](https://semver.org/), so anyone can check out or reference exactly what shipped.
- **CI/CD pipelines** — many deployment systems trigger builds or releases specifically off pushed tags matching a pattern like `v*`.
- **Generating changelogs** — `git log v1.1.0..v1.2.0 --oneline` shows exactly what changed between two tagged releases (see [[Viewing History with git log]]).
- **Signed tags for provenance** — projects with strict supply-chain security (e.g. requiring verified releases) use `git tag -s` so consumers can cryptographically confirm a release came from a trusted maintainer.
- **Marking a rollback point** — tagging "last known good" before a risky deploy gives a fast, unambiguous reference to return to if needed.

---

## ⚖️ Advantages

- Tags give permanent, human-readable names to exact commits — far more usable than communicating raw hashes.
- Annotated tags carry real metadata (author, date, message), useful for auditing and generating release notes later.
- Unlike branches, tags don't accidentally move forward, so a release reference stays trustworthy indefinitely.

---

## ⚠️ Limitations

- Tags are not pushed automatically — a very easy step to forget, leaving remote collaborators without a release marker you thought you'd shared.
- Lightweight tags carry no record of who created them or when, which can be a real gap for audit purposes on anything beyond a quick personal bookmark.
- Tags can technically be deleted and recreated pointing elsewhere, so they're a strong convention, not an absolute cryptographic guarantee, unless signed and verified.
- Too many tags on a fast-moving repo (e.g. tagging every build) can clutter `git tag` listings and make finding real releases harder.

---

## 🚨 Common Mistakes

- Creating a tag locally, pushing commits, and assuming the tag went along for the ride — it didn't; `git push --tags` or `git push origin <tagname>` is a separate, required step.
- Using lightweight tags for real releases instead of annotated ones, losing the message/author/date metadata that later turns out to matter.
- Confusing a tag with a branch and being surprised that `git commit` while "on" a tag doesn't move the tag forward (because checking out a tag detaches HEAD — see [[Understanding HEAD and Detached HEAD State]]).
- Deleting and recreating a tag that others have already fetched, creating confusion about which commit the tag "really" points to across different clones.
- Forgetting semantic versioning conventions and using inconsistent tag naming (`1.2`, `v1.2.0`, `release-1.2`) across a project's history, making automation and changelogs harder to build.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 2.6: Git Basics - Tagging
- Semantic Versioning specification (semver.org) — the near-universal convention for what tag names like `v1.2.0` mean
- `git help tag`

---

## 💡 Wisdom from Mímir

I treat `git push --follow-tags` as close to a default habit whenever I've just tagged a release — it pushes the commits and the annotated tag together in one step, so there's never a gap where the tag exists locally but nobody else can see it. The number of "wait, is v1.2.0 actually out?" conversations that one flag prevents is genuinely surprising.

---

## 🔗 Related Notes

- [[Understanding HEAD and Detached HEAD State]] — checking out a tag is a textbook example of entering detached HEAD
- [[Viewing History with git log]] — using tag ranges to generate a changelog between releases
- [[Working with Remotes - fetch, pull, and push]] — the push behavior tags deliberately opt out of by default
- [[Common Git Workflows - Feature Branch, Trunk-Based, and Git Flow]] — where release tagging typically fits in a team's overall process
