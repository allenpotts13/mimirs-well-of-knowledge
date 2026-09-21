# 🌊 Mímir's Well of Knowledge

**A living technical knowledge base — Obsidian vault, published.**

[![Live Site](https://img.shields.io/badge/live%20site-mimir.allenpotts.com-1a1a2e?style=for-the-badge)](https://mimir.allenpotts.com/)
[![Built with Obsidian](https://img.shields.io/badge/built%20with-Obsidian-483699?style=for-the-badge&logo=obsidian&logoColor=white)](https://obsidian.md)
[![Published with Obsidian Publish](https://img.shields.io/badge/published%20with-Obsidian%20Publish-6c5ce7?style=for-the-badge)](https://obsidian.md/publish)

> *Wisdom is earned through experience. Knowledge is preserved through discipline.*

**[→ Visit the live site](https://mimir.allenpotts.com/)**

---

## What This Is

Mímir's Well is a personal technical knowledge base, named for the Norse figure who guards the well of wisdom at the root of Yggdrasil — knowledge worth returning to, drawn from rather than consumed once and forgotten.

It is not a collection of copied documentation. Every note exists to answer four questions:

- **What** is it?
- **Why** does it matter?
- **When** should I reach for it?
- **What** did I learn from actually using it?

The whole project runs on one rule:

> **Never solve the same problem twice.**

This repository *is* the vault — the raw Markdown source that Obsidian Publish renders into the live site linked above. Editing happens locally in Obsidian; this repo is where it's version-controlled and deployed from.

---

## What's Inside

The Well is organized by domain, not by note type. Every topic gets the same shape: a **Codex** (landing page) linking out to **Concepts** (theory), a **Playbook** (task-oriented patterns and troubleshooting scenarios), a **Cheat Sheet** (dense syntax reference), and — where relevant — a **Data Analyst Reference**.

| Section | Covers | Notes |
|---|---|---|
| 💻 **Development** | SQL, Git, JavaScript, HTML, CSS, Java, Python | ~270 |
| 📖 **Learning** | Azure (AZ-900, DP-900), CompTIA A+ (Core 1 & 2), Network+, Security+ | ~175 |
| 📊 **Data Visualization** | Power BI (data modeling, DAX, Power Query) | ~35 |
| 🚑 **Troubleshooting** | PowerShell (AD, networking, Windows, hardware, Microsoft 365, SCCM) | ~65 |
| 🪟 **Microsoft** | Excel | growing |
| ☁️ **Cloud** | Azure fundamentals | growing |

Every certification-path note is grounded in the vendor's own official exam objectives, not written from memory. Every language/tool note is written from real, working code examples — not paraphrased vendor docs.

---

## How It's Organized

**The Codex pattern.** Every topic — a language, a certification, a tool — gets one landing page (its *Codex*) that groups links into the sections relevant to that topic: Concepts, Playbook, Cheat Sheet, Study Plan, Data Analyst Reference. The exact grouping flexes to fit the subject (a certification needs an exam-info page; a general-purpose language doesn't), but the entry point is always the same shape.

**One note, one idea.** Every Concept and Playbook note follows a fixed template — Purpose, Key Ideas, How It Works, Examples, Real World Applications, Advantages, Limitations, Common Mistakes, Wisdom, Related Notes — so any note in the vault is navigable the same way, regardless of topic.

**Runes as a visual language.** Elder Futhark runes mark note types at a glance: ᛟ for Concepts, ᚲ for Playbook/scenario notes, ᛊ for Cheat Sheets, ᛃ for Study Plans — plus a unique rune per Codex identifying its specific subject. It's a small thing, but it makes the site immediately recognizable as one coherent system rather than a pile of disconnected pages.

**Link, don't duplicate.** Wiki-links (`[[Note Name]]`) connect related concepts across the entire vault, resolved by title regardless of folder — a note can move without breaking anything that links to it.

Read the full philosophy: [**About the Well**](https://mimir.allenpotts.com/home/about-the-well).

---

## Tech Stack

- **[Obsidian](https://obsidian.md)** — the local-first Markdown editor this vault is authored in
- **[Obsidian Publish](https://obsidian.md/publish)** — renders this repository's Markdown into the live, hosted site
- **Custom theme** ([`publish.css`](./publish.css)) — a restrained Norse/deep-well visual identity: custom light/dark palettes, the rune system, a subtle ripple background, and a hand-tuned layout on top of Obsidian Publish's default rendering

---

## Repository Structure

```
🏠 Home/              Landing pages — The Well, the Knowledge Index, About the Well
💻 Development/        Languages & dev tools (SQL, Git, JS, HTML, CSS, Java, Python)
📖 Learning/           Certification study guides (Azure, CompTIA)
📊 Data Visualization/ Power BI
☁️ Cloud/              Azure fundamentals
🚑 Troubleshooting/    PowerShell
🪟 Microsoft/          Excel
publish.css            Site-wide theme (light/dark, runes, layout)
```

**Branches:**

- `main` — active development; the working copy of the vault
- `publish/v1*` — snapshots the live site is built from

---

## A Note on Reuse

This is a personal knowledge base, published openly so it might be useful — or at least interesting — to someone else. It isn't released under a formal open-source license; treat the content as reference and inspiration rather than something to fork wholesale. If a specific note or pattern is genuinely useful to you, borrow it and give it your own spin.

---

## The Oath

> Every note added to this vault should make tomorrow easier than today.
> Knowledge without organization is forgotten. Organization without understanding is empty.
>
> Build understanding. Preserve wisdom. Share experience.
> **Never solve the same problem twice.**
