---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Advanced
tags:
  - rebasing
  - history
  - cleanup
  - team-workflow
aliases:
  - git rebase -i
  - Interactive Rebase
  - Squash and Fixup
publish: true
---

# <span class="rune">ᛟ</span> Interactive Rebase for Cleaning Up History

> *Interactive rebase is a scripted rewrite of your own recent commits — a chance to edit the story of your work before anyone else has to read it.*

---

## 🎯 Purpose

Real work is messy. You commit "wip," then "actually fix wip," then "typo," then "ok now it really works." That's a completely normal way to work — but it's a terrible thing to hand to a reviewer or leave in permanent history. Interactive rebase exists to let you turn that messy, honest-to-yourself sequence of commits into a small number of clean, well-described, logical commits *before* anyone else has to look at it.

---

## 🧠 Key Ideas

- `git rebase -i <base>` opens an editable list of commits (from `<base>` up to your current tip) where you choose an action for each one.
- **pick** — keep the commit as-is.
- **reword** — keep the commit's changes, but stop to edit its message.
- **squash** — merge this commit's changes *and* message into the previous commit, then let you edit the combined message.
- **fixup** — like squash, but silently discards this commit's message and keeps the previous commit's message unchanged (the "I forgot something, glue it into the last commit and don't clutter the log" verb).
- **drop** — remove the commit entirely, as if it never happened.
- Just like plain rebase, interactive rebase rewrites commit hashes — so it carries the exact same golden rule: **only do this on local, unpushed commits** (or a branch you're certain nobody else has pulled).

---

## ⚙️ How It Works

Running `git rebase -i main` (or `HEAD~5` for "the last 5 commits") opens your editor with a plan that looks like a to-do list, oldest commit first:

```
pick a1b2c3d Add login form
pick e4f5g6h wip
pick h7i8j9k fix typo in wip
pick j1k2l3m actually fix the validation bug
pick m4n5o6p Update tests
```

You then edit the *verbs*, not the commits directly — Git reads your edited list back and replays each commit in order according to the verb you assigned:

```
pick a1b2c3d Add login form
fixup e4f5g6h wip
fixup h7i8j9k fix typo in wip
reword j1k2l3m actually fix the validation bug
pick m4n5o6p Update tests
```

Saving and closing this file kicks off the actual rebase: Git checks out the base commit, then replays each commit in the plan in order, applying each verb as it goes. The two `fixup` commits get folded silently into "Add login form" (their messages discarded). The `reword` commit pauses to let you rewrite its message — a good moment to turn "actually fix the validation bug" into something clearer like "Fix email validation regex missing plus-sign support." The result: five messy commits become two clean ones, with accurate messages, and no trace of the "wip" detour remains in history.

This works because, under the hood, interactive rebase is just automating the same mechanism as regular rebase: temporarily set the branch back to the base, then one at a time, cherry-pick each remaining commit forward — except now you get to reorder, merge, edit, or skip each step along the way.

---

## 💻 Examples

```bash
# Clean up your last 5 commits (still local, not yet pushed)
git rebase -i HEAD~5

# Or rebase everything since your branch diverged from main
git rebase -i main

# In the editor, the default plan is all "pick" — edit verbs as needed:
#   pick a1b2c3d Add login form
#   squash e4f5g6h wip
#   squash h7i8j9k fix typo in wip
#   reword j1k2l3m fix validation bug
#   pick m4n5o6p Update tests
#
# Save and close. If you used "squash", Git next opens an editor
# pre-filled with all three combined messages — trim it down to one
# clean summary + body before saving again.

# Reorder commits by simply reordering the lines before saving
#   pick m4n5o6p Update tests
#   pick a1b2c3d Add login form
# (Git will replay them in the new order — be careful of conflicts
#  if a later commit depends on an earlier one that's now been moved)

# Drop a commit entirely (e.g. an experimental change you decided against)
#   drop h7i8j9k Try caching approach (didn't work)

# If a conflict occurs mid-replay, resolve it like any other conflict,
# then continue the rebase instead of committing normally
git status              # see what needs resolving
# ... edit files, resolve markers ...
git add resolved-file.js
git rebase --continue

# Changed your mind partway through? Bail out completely, no harm done
git rebase --abort

# Quick fixup without opening the full interactive editor:
# stage a small correction, mark it as fixing an earlier commit...
git commit --fixup=a1b2c3d
# ...then let Git auto-arrange the squash for you
git rebase -i --autosquash a1b2c3d~1
```

---

## 🚀 Real World Applications

- **Pre-PR cleanup**: squashing a dozen "wip"/"fix"/"actually fix" commits into two or three well-described ones before opening a pull request.
- **Fixing an early typo**: using `reword` to correct a commit message several commits back without having to touch anything else.
- **Removing an abandoned experiment**: `drop`-ping a commit for an approach you tried and reverted, so it doesn't clutter permanent history.
- **Reordering for logical flow**: moving a small refactor commit ahead of the feature commit that depends on it, so the story reads in the right order.
- **`--fixup` + `--autosquash` workflow**: a common pattern for reviewers' requested changes — commit each fix with `--fixup=<commit>`, then squash them all into place automatically at the end.

---

## ⚖️ Advantages

- Produces a small number of well-organized, well-described commits instead of a raw, messy work log.
- `fixup` and `--autosquash` make it fast to fold small corrections into the right place without manually retyping messages.
- Gives you a final editorial pass before history becomes permanent and shared — much cheaper than trying to clean things up after the fact.
- `--abort` and the underlying reflog make it low-risk to experiment with, as long as it's confined to unpushed commits.

---

## ⚠️ Limitations

- Absolutely not safe on commits others have already pulled — it rewrites hashes exactly like plain rebase, with the same collaboration risks.
- Reordering commits can introduce conflicts that didn't exist in the original order, since each commit is replayed against a different intermediate state.
- Squashing too aggressively can erase useful granularity — sometimes several small logical commits are more valuable than one giant one.
- The interactive editor experience (verb list, conflict-by-conflict `--continue`) has a learning curve and can feel intimidating the first several times.

---

## 🚨 Common Mistakes

- Running interactive rebase on a branch that's already been pushed and pulled by someone else, then force-pushing over their work.
- Confusing `squash` and `fixup` — using `squash` when you actually wanted the earlier message kept unchanged, then having to manually clean up the merged message anyway.
- Forgetting `git rebase --continue` after resolving a conflict mid-rebase, and being confused why the rebase seems "stuck."
- Reordering commits without considering dependencies, causing conflicts (or worse, silently broken intermediate states) that wouldn't exist in the original order.
- Panicking and thinking work is lost when a rebase goes wrong — `git rebase --abort` (if still in progress) or the reflog (if already finished) almost always gets it back.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 7.6, "Git Tools: Rewriting History"
- `git help rebase` (see the "INTERACTIVE MODE" section for the full verb list)
- `git help commit` — the `--fixup` and `--squash` flags, paired with `rebase --autosquash`

---

## 💡 Wisdom from Mímir

I treat my own commit history as a rough draft until the moment I push it — and interactive rebase is the edit pass. Nobody expects a published article to include every false start and crossed-out sentence from the author's notebook; the same courtesy applies to commit history. The one rule I never break: once it's out in the world and someone else might have built on it, the draft is final. Edit freely before that line, never after it.

---

## 🔗 Related Notes

- [[Rebasing vs Merging]] — the broader rebase mechanics and the golden rule this note depends on
- [[Squashing Commits]] — a deeper look at the squash/fixup verbs specifically
- [[Writing Good Commit Messages]] — what to aim for when `reword`-ing or writing a squashed message
- [[Recovering Lost Commits with the Reflog]] — the safety net if an interactive rebase goes wrong after finishing
