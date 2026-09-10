---
type: concept
status: published
created: 2026-09-02
updated: 2026-09-02
technology: Git
difficulty: Intermediate
tags:
  - git-workflows
  - team-process
  - branching-strategy
aliases:
  - "Common Git Workflows: Feature Branch, Trunk-Based, and Git Flow"
  - Git Workflows
  - Branching Strategies
  - Trunk-Based Development
  - Git Flow
publish: true
permalink: git/common-git-workflows-feature-branch-trunk-based-and-git-flow
---

# <span class="rune">ᛟ</span> Common Git Workflows: Feature Branch, Trunk-Based, and Git Flow

> *A Git workflow is a team's agreed-upon convention for how branches get created, merged, and released — Git itself doesn't enforce any of them, which is exactly why picking one deliberately matters.*

---

## 🎯 Purpose

Git gives you branches, merges, and tags — but it has no opinion on *when* to branch, *how long* a branch should live, or *how* code reaches production. Left undecided, a team ends up with inconsistent, ad-hoc practices: some people branch for everything, some commit straight to `main`, releases happen whenever someone remembers to tag something.

Workflows exist to answer those questions once, as a team, so everyone's branching and merging habits are predictable and compatible with each other — and so tooling (CI, deploy pipelines, release notes) can rely on consistent structure.

---

## 🧠 Key Ideas

- **Feature Branch Workflow**: every feature or fix gets its own branch off `main`, developed in isolation, and merged back via a pull/merge request after review. This is the de facto default for most teams today, especially with GitHub/GitLab-style PR review baked into the culture.
- **Trunk-Based Development**: developers work in very short-lived branches (hours, not weeks) or commit directly to `main` ("trunk"), integrating constantly. Incomplete features are hidden behind **feature flags** rather than kept isolated on a long-lived branch.
- **Git Flow**: a heavier, structured model with dedicated long-lived branches — `main` (production), `develop` (integration), plus temporary `feature/*`, `release/*`, and `hotfix/*` branches — designed around scheduled release trains.
- The core tradeoff across all three is **integration frequency vs. isolation**: the longer code stays on its own branch before merging, the lower the day-to-day merge-conflict risk feels, but the bigger and scarier the eventual merge becomes.
- None of these are mutually exclusive absolutes — many real teams run "Feature Branch, but with short branches and frequent merges," which is really trunk-based development wearing a feature-branch hat.
- The right choice depends heavily on **team size, release cadence, and CI/CD maturity** — not on which workflow sounds most sophisticated.

---

## ⚙️ How It Works

**Feature Branch Workflow** is the shape most developers learn first: branch off `main` for a piece of work, commit there freely, open a pull request when ready, get it reviewed, merge it back. `main` is always meant to be in a releasable (or at least stable) state, and each feature's history stays visually grouped until it merges. It scales well from solo projects up to large teams, because it maps naturally onto code-review tooling — the PR *is* the unit of both branching and review.

**Trunk-Based Development** optimizes for continuous integration in the literal sense: integrating continuously. Instead of a feature living on its own branch for days or weeks, developers merge small, incremental changes into `main` constantly — sometimes multiple times a day — and use feature flags to hide unfinished functionality from users rather than hiding unfinished *code* on a branch. The payoff is that merge conflicts stay small (you're never far from everyone else's `main`) and CI is always validating something close to what will actually ship. The cost is that it demands real discipline: strong automated testing, a feature-flagging system, and a team culture comfortable with tiny, frequent commits landing on the branch everyone builds from.

**Git Flow** was designed for an era of scheduled, versioned releases — think desktop software with release notes and version numbers, not a SaaS product deploying twenty times a day. `develop` is the integration branch where features land; when it's time to ship, a `release/*` branch is cut from `develop` for final stabilization, then merged into both `main` and back into `develop`. Urgent production fixes get their own `hotfix/*` branches off `main`, merged back into both `main` and `develop` once resolved. It's thorough and gives very clear answers for "where does this code live right now," but it's also the heaviest of the three — more branch types, more merge points, more process to keep everyone aligned on.

---

## 💻 Examples

```bash
# --- Feature Branch Workflow ---
git switch -c feature/add-login-page main
# ... work, commit ...
git push -u origin feature/add-login-page
# Open a pull request against main, get it reviewed, then merge
# main stays deployable at all times; the branch is deleted after merging

# --- Trunk-Based Development ---
git switch -c fix/typo main       # short-lived — hours, not weeks
# ... small, complete change ...
git push -u origin fix/typo
# Fast review, fast merge back to main — same day, ideally same hour
git switch main && git pull

# Unfinished feature? Hide it behind a flag instead of a long-lived branch
if (featureFlags.isEnabled("new-checkout-flow")) {
  renderNewCheckout();
} else {
  renderLegacyCheckout();
}
# The incomplete code ships to main and to production, but stays inert
# for real users until the flag is flipped on

# --- Git Flow ---
git switch -c develop main                     # long-lived integration branch
git switch -c feature/payment-retry develop     # feature branches off develop
# ... work, merge feature back into develop when done ...

git switch -c release/2.4.0 develop             # cut a release branch to stabilize
# bug fixes only on the release branch, then:
git switch main
git merge --no-ff release/2.4.0
git tag v2.4.0
git switch develop
git merge --no-ff release/2.4.0                 # bring fixes back into develop

# Urgent production bug — hotfix branches directly off main
git switch -c hotfix/critical-auth-bug main
# ... fix ...
git switch main && git merge --no-ff hotfix/critical-auth-bug
git switch develop && git merge --no-ff hotfix/critical-auth-bug
```

---

## 🚀 Real World Applications

- **Small startups and fast-moving SaaS teams**: usually gravitate toward Feature Branch (lightweight version) or full Trunk-Based Development, since they deploy continuously and can't afford Git Flow's ceremony.
- **Open source projects**: almost universally use Feature Branch Workflow via forks and pull requests — it maps perfectly onto contributor review from people who don't have direct write access.
- **Large, established companies with scheduled releases**: enterprise software, embedded/firmware, or regulated industries (where releases go through formal QA/compliance sign-off) often still use something Git-Flow-shaped, because "what exactly is in release 4.2" needs a clear, durable answer.
- **High-velocity engineering orgs (Google, Meta, and similar)**: are the poster children for trunk-based development, specifically because their CI/testing/feature-flag infrastructure is mature enough to make constant integration safe.
- **Mixed reality**: many real teams run a hybrid — Feature Branch structurally, but with a strong cultural push to keep branches short-lived, which is trunk-based development in spirit even if PRs are still used for review.

---

## ⚖️ Advantages

- **Feature Branch**: familiar, maps cleanly onto PR-based code review, low ceremony, works for teams of almost any size.
- **Trunk-Based**: smallest possible merge conflicts, fastest feedback from CI, keeps `main` always close to what's really being tested.
- **Git Flow**: very explicit about where in the release lifecycle any given piece of code is — great traceability for versioned, scheduled releases.

---

## ⚠️ Limitations

- **Feature Branch**: branches can quietly grow long-lived if reviews stall, leading back to painful, Git-Flow-sized merges anyway.
- **Trunk-Based**: requires serious investment in automated testing and feature flagging up front — without that safety net, constant direct integration to `main` becomes constant breakage.
- **Git Flow**: heavy for teams that release continuously — the `develop`/`release`/`hotfix` branch juggling is overhead that fast-shipping teams often find actively slows them down.

---

## 🚨 Common Mistakes

- Adopting Git Flow by default "because it's thorough," without actually having a release-train cadence that needs it — most teams shipping continuously find it adds friction rather than removing it.
- Calling a workflow "trunk-based" while still letting feature branches live for weeks — trunk-based development is defined by integration *frequency*, not by which workflow diagram is on the wiki.
- Letting feature branches drift far from `main` in the Feature Branch Workflow, turning what should be a routine merge into a multi-day conflict-resolution project.
- Picking a workflow based on what a blog post or a previous employer used, rather than the team's actual size, release cadence, and CI maturity.
- Mixing workflow conventions inconsistently across a team — half using Git Flow branch names, half doing ad-hoc feature branches — which erodes the whole point of having a shared convention.

---

## 📖 Further Reading

- Vincent Driessen's original "A successful Git branching model" post — the canonical source for Git Flow
- Atlassian's Git Workflows documentation — clear practical comparisons of Feature Branch, Gitflow, and Forking workflows
- Martin Fowler's writing (and trunkbaseddevelopment.com) on Trunk-Based Development and its relationship to continuous integration

---

## 💡 Wisdom from Mímir

The question I'd ask a team before picking a workflow isn't "which one is best" — it's "how often do we actually release, and how good is our test suite." Git Flow answers questions that only matter if you ship on a schedule with formal versioning; trunk-based development only works if your CI can catch problems fast enough to make constant direct integration safe. A workflow chosen to match your team's real rhythm disappears into the background. A workflow chosen because it looked impressive in a blog post becomes the thing everyone quietly works around.

---

## 🔗 Related Notes

- [[Branching with git branch and git switch]] — the underlying mechanics every one of these workflows is built on top of
- [[Merging Branches]] — how the "bring it back together" step actually works in each model
- [[Rebasing vs Merging]] — relevant to how clean or messy history looks under Trunk-Based vs. Git Flow style integration
