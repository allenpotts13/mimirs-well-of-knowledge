---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - git-remotes
  - rewriting-history
  - safety
aliases:
  - force-with-lease
  - Safe Force Push
  - git push --force-with-lease
publish: true
permalink: git/force-pushing-safely-with-force-with-lease
---

# <span class="rune">ᛟ</span> Force-Pushing Safely with --force-with-lease

> *`--force-with-lease` force-pushes only if the remote branch still looks the way it did the last time you fetched it — turning a blind overwrite into a checked one.*

---

## 🎯 Purpose

Rewriting history — via interactive rebase, amending a commit, or resetting a branch — changes commit hashes, which means a plain `git push` will be rejected because your local branch and the remote branch have "diverged." The blunt fix, `git push --force`, works, but it works *too* well: it overwrites whatever is on the remote unconditionally, even if a teammate pushed new commits to that branch five minutes ago that you haven't even seen yet.

`--force-with-lease` exists to give you the power of force-pushing (necessary after legitimate history rewrites) without the landmine of silently erasing someone else's work.

---

## 🧠 Key Ideas

- `git push --force` overwrites the remote branch unconditionally, no matter what's there — including commits you've never fetched and don't know exist.
- `git push --force-with-lease` first checks: "does the remote branch still point where I last saw it point (via my last fetch)?" If yes, it proceeds. If the remote has moved because someone else pushed, it **refuses** the push instead of clobbering it.
- The "lease" is essentially a compare-and-swap: your local knowledge of the remote's tip is the expected value, and the push only succeeds if that expectation still holds true.
- Force-pushing at all is only appropriate on branches you effectively own — your own feature branch, not `main` or any shared/protected branch.
- `--force-with-lease` still requires that you've fetched recently — it can't protect you against changes it doesn't know about, so it's a safety net, not a substitute for communication.

---

## ⚙️ How It Works

Normally, `git push` is a fast-forward-only operation: Git will only accept your push if your local branch's history is a direct continuation of the remote's. Any history-rewriting operation — `git commit --amend`, `git rebase`, `git reset` followed by new commits — changes the commit hashes of everything after the rewrite point, so your branch is no longer a fast-forward of the remote. Git (rightly) rejects the push to protect against accidental data loss.

Plain `--force` bypasses this check entirely: "whatever I have locally, make the remote match it, full stop." That's dangerous if the remote has commits your local copy doesn't — those commits simply vanish from the branch, with no warning, no confirmation, and often no easy way for the person who pushed them to know what happened.

`--force-with-lease` adds a condition: before pushing, Git compares the remote-tracking branch's current tip (as recorded the last time you fetched) against what the remote actually reports right now. If they match, your assumption about the remote's state was correct, and the force-push proceeds. If they don't match — meaning someone pushed something you don't have — Git aborts with an error instead of overwriting it. It's the difference between "I'm confident nothing has changed since I last looked, and I'm right" versus "I'm just going to assume nothing changed and hope for the best."

---

## 💻 Examples

```bash
# Scenario: you just cleaned up your feature branch with interactive rebase
git rebase -i HEAD~5
# Commit hashes for the last 5 commits have all changed

git push
# REJECTED — remote has the old commits, local has new ones, not a fast-forward

# The blunt, risky way — overwrites the remote no matter what's there
git push --force
# Works, but if a teammate pushed to this branch in the meantime,
# their commits are now gone from the remote with zero warning

# The safe way — checks the remote hasn't moved since your last fetch first
git push --force-with-lease
# If the remote matches what you last fetched: push succeeds
# If someone else pushed since your last fetch: push is REJECTED with an error

# Typical rejection message when the lease check fails:
# ! [rejected] feature/login -> feature/login (stale info)
# error: failed to push some refs...

# The correct response to that rejection: fetch, see what changed, then decide
git fetch origin
git log origin/feature/login --oneline -5
# Now you can rebase on top of their work, or coordinate, instead of
# blindly overwriting it

# Extra-safe variant: explicitly pin the expected remote commit
# (useful in scripts/CI where you want zero ambiguity about "last known state")
git push --force-with-lease=feature/login:a1b2c3d
```

---

## 🚀 Real World Applications

- **Cleaning up commit history before merging a PR**: interactive rebase to squash/reword commits on your own feature branch, then `--force-with-lease` to update the remote copy of that PR branch safely.
- **Amending a commit after review feedback**: `git commit --amend`, then `--force-with-lease` to push the fix without creating a noisy "fix review comments" follow-up commit.
- **Rebasing a feature branch onto an updated main**: after `git rebase main`, the feature branch's history has changed and needs a force-push — `--force-with-lease` protects against clobbering a co-author's concurrent push to the same branch.
- **CI/CD pipelines that rewrite and push tags or branches**: automated systems benefit from the pinned form (`--force-with-lease=<branch>:<expected-sha>`) for a guaranteed, race-free check.

---

## ⚖️ Advantages

- Prevents the single most common form of "someone's work just vanished" incident on shared branches.
- Requires no extra communication overhead in the common case — it just silently succeeds when nothing has changed.
- Fails loudly and safely instead of failing silently, which makes it a good default habit to build instead of reaching for plain `--force`.

---

## ⚠️ Limitations

- Only protects you if your local remote-tracking state is reasonably fresh — if you haven't fetched in a while, the "lease" you're checking against is stale and less meaningful.
- Doesn't eliminate the need for good judgment — force-pushing to a genuinely shared/protected branch (like `main`) is still a bad idea regardless of which flag you use.
- Many teams set up branch protection rules that block force-pushes entirely on certain branches — `--force-with-lease` doesn't override server-side policy, nor should it need to.
- Slightly more to type/remember than `--force`, which is exactly why so many people default to the unsafe version out of habit.

---

## 🚨 Common Mistakes

- Reaching for `git push --force` out of muscle memory instead of `--force-with-lease`, especially under deadline pressure.
- Force-pushing to `main` or another shared branch at all — force-pushing should be reserved for branches only you (or your immediate collaborators, with coordination) are working on.
- Assuming `--force-with-lease` makes force-pushing "always safe" and skipping communication with collaborators on a shared feature branch — it prevents silent data loss, but a rejected push still needs a human decision about how to proceed.
- Not fetching before force-pushing, which makes the lease check compare against stale information and defeats much of its purpose.
- Panicking and re-running with plain `--force` the moment `--force-with-lease` gets rejected — that rejection is the safety mechanism working correctly, not a bug to route around.

---

## 📖 Further Reading

- `git help push` — see the `--force-with-lease` section for the full flag semantics, including the pinned-SHA form
- *Pro Git* (free online book) — Chapter 3.5: Git Branching - Rebasing (covers why history rewrites require force-pushing)

---

## 💡 Wisdom from Mímir

I think of `--force-with-lease` as Git asking "are you sure nothing changed since you last looked?" and then actually checking your answer instead of taking your word for it. Plain `--force` is you asserting that with total confidence and no verification — which is exactly the kind of confidence that precedes the worst kind of incident report. Make `--force-with-lease` your fingers' default, and reserve plain `--force` for the rare moment you've deliberately decided you don't even want the safety check.

---

## 🔗 Related Notes

- [[Interactive Rebase for Cleaning Up History]] — the most common legitimate reason you'll need to force-push at all
- [[Working with Remotes - fetch, pull, and push]] — the fetch/push mechanics that the lease check relies on
- [[Rebasing vs Merging]] — background on why rebasing rewrites history and merging doesn't
