---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - rebasing
  - merging
  - history
  - team-workflow
aliases:
  - git rebase
  - Rebase vs Merge
  - The Golden Rule of Rebasing
publish: true
---

# <span class="rune">ᛟ</span> Rebasing vs Merging

> *Merge tells the truth about how history actually happened; rebase tells a cleaner, more convenient lie — and picking between them is really about which one you value more for a given branch.*

---

## 🎯 Purpose

Both `git merge` and `git rebase` solve the same underlying problem — combining commits from one branch with another — but they produce fundamentally different results in your history. Neither is objectively "correct." This concept exists because teams genuinely disagree about which to use as a default, and that disagreement usually comes down to a real tradeoff, not a matter of taste alone.

---

## 🧠 Key Ideas

- **Merge** creates a new commit with two parents, preserving the true, literal shape of how branches diverged and rejoined.
- **Rebase** takes your branch's commits and *replays* them one by one on top of a new base, producing new commits (new hashes) and a straight, linear history — as if you'd branched off much later than you actually did.
- Rebase rewrites history — every commit it replays gets a brand-new SHA, even if the content is identical.
- **The golden rule of rebasing**: never rebase commits that other people already have — i.e., never rebase a branch that's already been pushed and pulled by someone else, or shared publicly.
- Teams split roughly into "merge camp" (value an honest historical record, avoid rewriting shared history) and "rebase camp" (value a clean, linear, easy-to-read log) — both are legitimate priorities, which is exactly why the debate persists.
- A common middle ground: rebase freely on your own local, unpushed branch to clean it up, then merge (often with `--no-ff`) into the shared branch.

---

## ⚙️ How It Works

Imagine `main` gained two new commits while you were working on `feature-x`, which also has two commits of its own, both starting from the same point.

**Merge** says: "Let's keep both stories exactly as they happened." It creates a new merge commit with two parents — one pointing at `main`'s tip, one at `feature-x`'s tip — so the log literally shows two lines converging. Nothing about the four existing commits changes.

**Rebase** says: "Let's pretend `feature-x` was written *starting from* where `main` currently is." Git temporarily removes `feature-x`'s two commits, fast-forwards to `main`'s current tip, then reapplies those two commits one at a time on top — but because their parent commit is now different, each one gets a new hash. The result reads as one clean, straight line: `main`'s commits, then `feature-x`'s commits, with no fork ever visible.

This replay is exactly why the golden rule exists. If someone else already has the *original* `feature-x` commits (because you pushed and they pulled), and you rebase and force-push new ones with different hashes, their local history and yours no longer agree — Git sees them as entirely unrelated commits that happen to have similar content. Their next `pull` either creates a tangled duplicate mess or requires a forced sync, and any work they'd built on top of the old commits gets orphaned. Rebasing is safe exactly to the extent that nobody else has built anything on the commits you're about to erase and replace.

---

## 💻 Examples

```bash
# MERGE approach: preserves both histories, creates a merge commit
git switch main
git merge feature-x
# Log shows a fork-and-rejoin shape; feature-x's original commits untouched

# REBASE approach: replays feature-x's commits on top of main's current tip
git switch feature-x
git rebase main
# feature-x's commits are rewritten with new hashes, now sitting
# linearly on top of main's latest commit

git switch main
git merge feature-x
# Because feature-x is now a direct descendant of main, this is a
# fast-forward — no merge commit needed, history stays linear

# Golden rule in action: this is SAFE — feature-x is still only local
git log feature-x   # nobody else has pulled these commits yet
git rebase main     # fine, rewrite away

# This is DANGEROUS — feature-x was already pushed and a teammate pulled it
git push origin feature-x        # teammate now has these exact commits
# ... time passes, teammate built work on top ...
git rebase main                  # rewrites commits teammate already has
git push --force origin feature-x   # teammate's history now conflicts with yours

# A common safe middle ground: rebase locally to clean up, THEN push once
git switch feature-x
git rebase -i main        # clean up your own unpushed commits
git push origin feature-x # first push — nobody else has seen the old versions
```

---

## 🚀 Real World Applications

- **Personal feature branches**: rebasing your own unpushed branch onto the latest `main` before opening a PR, so the PR diff is clean and easy to review.
- **Open-source contributions**: many maintainers ask contributors to rebase their PR branch before merge, to keep the mainline history linear and readable.
- **Shared long-lived branches**: teams working on a shared `develop` or release branch almost always default to merge, precisely because the golden rule makes rebase unsafe there.
- **Trunk-based development**: some teams enforce "rebase before merge" as policy specifically to keep `main`'s history a straight, bisectable line.
- **Debugging with `git bisect`**: a linear, rebased history makes bisecting between good and bad commits far easier to reason about than one riddled with merge commits.

---

## ⚖️ Advantages

**Merge:**
- Never rewrites history — always safe on shared/pushed branches.
- Preserves an honest record of exactly when and how work actually branched and rejoined.
- Conflict resolution happens once, at the merge point.

**Rebase:**
- Produces a clean, linear, easy-to-read history with no merge-commit noise.
- Makes `git bisect` and `git log` straightforward to follow.
- Lets you clean up messy "wip" commits before they become permanent shared history (paired with interactive rebase).

---

## ⚠️ Limitations

**Merge:**
- History can get cluttered with many merge commits, especially with frequent small merges.
- The "true shape" of history is sometimes messier and less useful to read than a curated linear one.

**Rebase:**
- Absolutely unsafe on any branch other people have already pulled from, per the golden rule.
- Can require resolving the *same* conflict multiple times if several commits touch the same lines, since each commit is replayed individually.
- Requires a force-push to update a remote branch you've already pushed once, which itself carries risk.

---

## 🚨 Common Mistakes

- Rebasing a branch that's already shared, then force-pushing over teammates' work without warning — the single most common source of "my Git history is a disaster" incidents.
- Rebasing `main` itself (or any shared trunk) instead of rebasing your feature branch *onto* it — always rebase the branch with less shared history, not the shared trunk.
- Confusing "rebase rewrites the past" with "rebase loses work" — the original commits aren't gone (reflog still has them), but anyone relying on the old hashes will have a bad time.
- Treating the merge-vs-rebase debate as a moral question rather than a team/context decision — the right answer depends on whether the branch is shared and what the team values.
- Force-pushing with a plain `--force` after a rebase instead of `--force-with-lease`, which risks silently overwriting a teammate's more recent push you didn't know about.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 3, "Rebasing"
- Atlassian Git Tutorials — "Merging vs. Rebasing" (a widely referenced comparison)
- `git help rebase` (see the "NOTES" section on rebasing public history)

---

## 💡 Wisdom from Mímir

I've stopped thinking of merge and rebase as competitors and started thinking of them as tools for different *audiences*. Rebase is for an audience of one — you, tidying your own workspace before anyone else sees it. Merge is for an audience of everyone — a permanent, honest record once work becomes shared. The golden rule isn't really a rule about rebase at all; it's a rule about respecting the moment history stops being yours alone to rewrite.

---

## 🔗 Related Notes

- [[Merging Branches]] — the alternative approach this note contrasts against
- [[Interactive Rebase for Cleaning Up History]] — using rebase specifically to curate local commits
- [[Force-Pushing Safely with --force-with-lease]] — the safer way to push after a legitimate rebase
- [[Recovering Lost Commits with the Reflog]] — how to recover commits a rebase appeared to "lose"
