---
type: concept
status: active
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - git-history
  - rebasing
  - commit-hygiene
aliases:
  - Squash Commits
  - git merge --squash
  - Combining Commits
---

# 📚 Squashing Commits

> *Squashing takes several commits and folds them into one — trading the messy, real-time story of how you got somewhere for a single, clean statement of what actually changed.*

---

## 🎯 Purpose

Real development is messy: "wip," "fix typo," "actually fix it this time," "address review comments" — a feature branch often accumulates a dozen small, unglamorous commits before it's ready to merge. That history is useful *while you're working*, but it's often noise to everyone else looking at the project's permanent record later.

Squashing exists to bridge that gap — letting you commit freely and imperfectly as you work, then consolidate that mess into one (or a few) clean, meaningful commits before it becomes part of the shared, permanent history.

---

## 🧠 Key Ideas

- Squashing combines multiple commits into a single commit, with a single new commit message, discarding the individual commit boundaries in between.
- There are two common ways to do it: **interactive rebase** (`git rebase -i`, using `squash` or `fixup` on individual commits) and **`git merge --squash`** (squashing an entire branch into one commit at merge time).
- In an interactive rebase, `squash` combines a commit into the previous one *and* lets you edit the combined commit message; `fixup` does the same but silently discards the fixup commit's message entirely, keeping the earlier message untouched.
- `git merge --squash <branch>` takes all the changes from a branch and stages them as one commit's worth of change on the current branch — but notably does **not** create a merge commit or record the branch as merged in the parent's history at all.
- The core tradeoff is **clean, readable history vs. lost granularity** — squashed history is easier to skim in `git log`, but the fine-grained commits that could have helped `git blame` or `git bisect` pinpoint an exact change are gone for good.

---

## ⚙️ How It Works

Interactive rebase gives you the most control: it opens an editable list of the commits being rebased, and for each one you choose an action. `pick` keeps a commit as-is. `squash` merges it into the commit immediately above it in the list, and then opens an editor letting you write a new, combined commit message drawing on both. `fixup` does the same merge, but throws away the fixup commit's message entirely — perfect for those "oops, typo" or "address PR feedback" commits whose messages add no value and would just clutter the final combined message. Working through a branch's history with a mix of `pick`, `squash`, and `fixup` lets you turn ten scrappy commits into one or two well-described ones, each telling a real, coherent part of the story.

`git merge --squash` works at a coarser level: instead of picking and choosing individual commits, it looks at the *entire diff* between the branch you're merging and your current branch, and stages that entire diff as if it were one giant change — leaving it to you to `git commit` it as a single new commit. Critically, this is not a real merge in Git's eyes: no merge commit is created, and the branch's own commit history is never linked into the target branch's ancestry. This is popular for platforms like GitHub's "Squash and merge" PR button — the PR's branch can have as many commits as it wants, and the target branch (often `main`) only ever gets one tidy commit per PR.

Either way, the underlying tradeoff is the same: you're compressing the "how I got here, in real time" story into "here's what actually changed." That compression is often exactly what you want for a shared history — but it does throw away information.

---

## 💻 Examples

```bash
# --- Interactive rebase: squash the last 4 commits into 1 ---
git log --oneline -4
# a1b2c3d Address PR feedback
# e4f5g6h Fix typo in validation
# i7j8k9l wip
# m1n2o3p Add password strength validation

git rebase -i HEAD~4
# Editor opens with:
#   pick m1n2o3p Add password strength validation
#   pick i7j8k9l wip
#   pick e4f5g6h Fix typo in validation
#   pick a1b2c3d Address PR feedback
#
# Change to:
#   pick   m1n2o3p Add password strength validation
#   fixup  i7j8k9l wip                     <- message discarded, silently merged
#   fixup  e4f5g6h Fix typo in validation   <- message discarded, silently merged
#   squash a1b2c3d Address PR feedback      <- message kept, editable
#
# Save and close — Git applies each action in order

# Since "squash" was used on the last one, an editor opens to write
# the final combined commit message, e.g.:
# "Add password strength validation"

git log --oneline -1
# One clean commit now stands in place of the original four

# --- git merge --squash: squash an entire feature branch at merge time ---
git switch main
git merge --squash feature/password-validation
# All changes from the branch are staged, but NOT committed yet,
# and NO merge commit is created

git status
# Shows everything from the feature branch staged as one big change set

git commit -m "Add password strength validation"
# Now main has exactly one new commit representing the whole feature

# Note: the feature branch's own commit history is untouched and still
# exists on that branch — it's just not linked into main's ancestry
```

---

## 🚀 Real World Applications

- **"Squash and merge" PR buttons on GitHub/GitLab/Bitbucket**: the default merge strategy for many teams, so `main`'s history reads as one commit per feature/PR rather than every intermediate "wip" commit.
- **Cleaning up a branch before requesting review**: using interactive rebase to squash exploratory or fixup commits into the meaningful ones, so reviewers see a coherent, reviewable diff rather than the real-time mess of how it was built.
- **Consolidating "fix review comments" commits**: after a round of PR feedback, squashing those follow-up commits back into the original relevant commit rather than leaving a trail of "address comments" noise in permanent history.
- **Preparing a clean commit for `git bisect`**: sometimes squashing several commits into one *before* it goes on trunk actually makes future bisecting easier, since bisect will land on one coherent, testable change instead of an intermediate broken state.

---

## ⚖️ Advantages

- Produces a `git log` that reads like a coherent story of meaningful changes, instead of a raw diary of every intermediate step.
- Makes code review easier by letting contributors commit messily and often, then present one clean diff at the end.
- Keeps `main`'s history free of "wip"/"fix typo"/"oops" commits that add no long-term value.

---

## ⚠️ Limitations

- Destroys the fine-grained history that `git blame` and `git bisect` could otherwise use to pinpoint exactly which small change introduced a problem — everything in the squash gets attributed to one commit.
- `git merge --squash` doesn't preserve any record that a merge happened at all — no merge commit, no link back to the source branch's history — which can make later auditing ("when did this branch actually get merged") harder.
- Squashing commits that have already been pushed and shared rewrites history, which requires a force-push and can disrupt collaborators exactly the way any history rewrite does.
- Over-squashing (combining genuinely unrelated changes into one commit "for tidiness") can produce a commit that's hard to review, hard to revert cleanly, and hard to understand later.

---

## 🚨 Common Mistakes

- Squashing commits that a teammate has already pulled and built on top of, causing painful divergent-history conflicts for them.
- Losing meaningful intermediate commits by squashing too aggressively — sometimes a few well-separated commits communicate the change better than one giant one.
- Writing a lazy final commit message after squashing ("various fixes") instead of taking the opportunity to write one *good* message that actually explains the combined change.
- Using `fixup` when the discarded commit's message actually had useful context — worth a quick check before assuming a message is disposable.
- Forgetting that `git merge --squash` requires an explicit `git commit` afterward — the squashed changes just sit staged until you do.

---

## 📖 Further Reading

- *Pro Git* (free online book) — Chapter 7.6: Git Tools - Rewriting History
- `git help rebase` — see the `--interactive` section for the full `squash`/`fixup` semantics
- `git help merge` — see the `--squash` option

---

## 💡 Wisdom from Mímir

I think of commit history as having two audiences with different needs: *you, right now*, who benefits from committing early and often as a personal checkpoint system, and *everyone later*, who benefits from a history that reads like a well-edited story rather than a stream of consciousness. Squashing is how you serve both — commit like nobody's watching while you work, then squash like everybody's reading before you share it. The only real trap is squashing away detail you'll wish you still had the day you're bisecting a regression at 11pm.

---

## 🔗 Related Notes

- [[Interactive Rebase for Cleaning Up History]] — the primary mechanism (`squash`/`fixup`) for doing this deliberately, commit by commit
- [[Merging Branches]] — background on how `git merge --squash` differs from a normal merge
- [[Writing Good Commit Messages]] — squashing is often exactly the moment to write the good message you skipped while working
