---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - undoing-changes
  - shared-history
  - collaboration
  - safety
aliases:
  - git revert
  - Undoing a Pushed Commit
publish: true
---

# 📚 Reverting a Commit vs Resetting

> *`git revert` undoes a commit by adding a new one that cancels it out; `git reset` undoes a commit by pretending it never existed — and once history is shared with anyone else, that difference is the whole ballgame.*

---

## 🎯 Purpose

Sooner or later you'll need to undo a commit that's already been pushed — a bug slipped into `main`, a bad merge went out, someone force-pushed something regrettable. The instinct many beginners reach for is `git reset --hard`, because that's the "undo" command they already know from [[Undoing Changes - checkout, restore, and reset]]. On a shared branch, that instinct is exactly wrong.

`git revert` exists for this precise situation: it undoes the *effect* of a commit without erasing the commit itself from history, so it's safe to use on branches other people have already pulled. Knowing when to reach for `revert` instead of `reset` is one of the clearest signals of Git maturity on a team.

---

## 🧠 Key Ideas

- `git revert <commit>` creates a **brand-new commit** whose changes are the exact opposite of `<commit>`, leaving the original commit intact in history.
- `git reset <commit>` **moves the branch pointer** backward, effectively making it look like the commits after `<commit>` never happened on this branch.
- Revert is **additive** — history only ever grows, nothing is destroyed or rewritten. This makes it safe for anyone who has already pulled the commit you're undoing.
- Reset is **subtractive/rewriting** — it changes what commits your branch pointer includes, which becomes dangerous the moment someone else has already based work on the commits you're removing.
- The rule of thumb: **if it's been pushed and others might have it, revert. If it's still local and only yours, reset is fine.**
- `git revert` can also revert a *merge commit*, but requires an extra `-m` flag to specify which parent's history to treat as "mainline" — a common trip-up.

---

## ⚙️ How It Works

Think of your commit history as a shared, published diary that other people are reading along with you (this is the "shared" part — anyone who has pulled your branch has their own copy of every commit in it).

`git reset` tears pages out of *your* diary. If nobody else has read those pages yet, fine — no harm done. But if others already copied those pages into their own diary (pulled the commits), and you tear them out and rewrite what comes next, your diary and theirs now permanently disagree. The next time they try to sync, Git has no clean way to reconcile "you removed pages I still have" — this is the diverging-history mess that force-pushes cause.

`git revert` instead writes a *new* diary entry that says "ignore what I wrote on page 12, actually do the opposite." Nothing is torn out. Everyone's diary stays in agreement about what happened and when — it's just that the story now includes both the mistake and its correction, which is honest and exactly what history is for.

---

## 💻 Examples

```bash
# --- Reverting the most recent commit ---

git log --oneline
# a1b2c3d  Add broken feature flag logic   <- this one needs to go
# e4f5g6h  Fix typo in README

git revert a1b2c3d
# Opens an editor for the revert commit message (auto-filled), then creates
# a NEW commit that undoes exactly what a1b2c3d changed.
# History now shows both the original commit and its revert — nothing erased.

git push
# Safe to push — this is a normal, forward-moving commit like any other

# --- Reverting without opening an editor ---

git revert --no-edit a1b2c3d
# Uses the auto-generated message ("Revert 'Add broken feature flag logic'")

# --- Reverting a merge commit ---

git log --oneline --graph
git revert -m 1 <merge-commit-hash>
# -m 1 tells Git to treat the first parent (usually the branch you merged
# INTO) as mainline, so the revert undoes the merge's changes correctly

# --- Reverting a range of commits ---

git revert HEAD~3..HEAD
# Reverts the last 3 commits, oldest first, each as its own revert commit

# --- Contrast: reset, only safe because nothing has been pushed yet ---

git log --oneline
git reset --hard HEAD~1
# Fine here — this commit only exists on your machine so far
```

---

## 🚀 Real World Applications

- **A bug reaches production** — reverting the offending commit on `main` gets a fix out immediately without needing to untangle a manual patch, and preserves a clear record that "this happened, and here's the fix."
- **Rolling back a bad deploy** — many teams `git revert` the merge commit that introduced the regression rather than trying to hand-craft the opposite change.
- **Open-source / public repos** — since anyone could have already cloned or forked a commit, `revert` is effectively the *only* safe way to undo something once it's on the public default branch.
- **Audit trails** — in regulated environments, being able to show that a mistake was made *and* explicitly corrected (rather than silently erased) is often a compliance requirement.
- **Undoing a merge that turned out to be premature** — reverting the merge commit backs out an entire feature cleanly in one step.

---

## ⚖️ Advantages

- Never rewrites history, so it's always safe on shared/pushed branches.
- Leaves a clear, honest audit trail — the mistake and its fix are both visible in `git log`.
- Works well with code review — a revert is just a normal PR/commit that can be reviewed like any other change.

---

## ⚠️ Limitations

- Doesn't remove the "clutter" of the original commit from history the way `reset` would — history grows rather than shrinks.
- Reverting a merge commit is genuinely more confusing (the `-m` mainline-parent flag trips up even experienced users).
- If later commits depend on the one you're reverting, you may hit conflicts that need manual resolution, just like a merge.
- Reverting an old commit that's been built upon extensively can sometimes only partially undo the intended effect, since later commits may have layered more changes on top.

---

## 🚨 Common Mistakes

- Running `git reset --hard` on a branch that's already been pushed and pulled by teammates, causing painful divergence and forcing others into confusing recovery steps.
- Reverting a merge commit without the `-m` flag and getting an error (or worse, an unintended result).
- Assuming `revert` deletes the bad commit from history — it doesn't; the original commit is still there, just canceled out by the new one.
- Forgetting that reverting old commits can conflict with newer changes, and then being surprised when a "simple undo" needs manual conflict resolution.
- Reaching for `reset` out of habit because it's the more familiar command, without stopping to check whether the commit has already been shared.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 7.7: Git Tools - Reset Demystified (contrasts reset with revert)
- `git help revert`

---

## 💡 Wisdom from Mímir

I ask myself one question before undoing anything on a branch: *has anyone else's Git already seen this commit?* If yes — even just one teammate, even just one CI pipeline that fetched it — revert. If no, meaning it's still sitting only on my machine and nobody has pulled it, reset is completely fine and often tidier. The commit itself doesn't know or care which tool undoes it; the only thing that matters is who else might already be holding a copy.

---

## 🔗 Related Notes

- [[Undoing Changes - checkout, restore, and reset]] — the local, pre-push undo toolkit this note contrasts against
- [[Understanding the Three Trees]] — background for how `reset` moves the repository pointer
- [[Merging Branches]] — needed context for reverting merge commits with `-m`
- [[Force-Pushing Safely with --force-with-lease]] — what happens when `reset` on a shared branch forces a push anyway
- [[Recovering Lost Commits with the Reflog]] — the safety net if a `reset` on shared history already caused trouble
