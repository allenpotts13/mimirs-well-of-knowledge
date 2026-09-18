---
type: exam-scenario
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Intermediate
tags:
  - security-plus
  - general-security-concepts
  - cryptography
aliases:
  - Cryptographic Solution Choice
publish: true
permalink: comptia-security-sy0-701/choosing-the-right-cryptographic-solution
---

# <span class="rune">ᚲ</span> Choosing the Right Cryptographic Solution

> *Confidentiality, integrity, and authenticity are three different guarantees — pick the tool built for the one the scenario actually needs.*

---

## 🎯 Problem

A scenario describes a data protection or authenticity requirement. Choose the correct cryptographic technique.

---

## 🤔 Mental Model

| Signal in the scenario | Answer |
|---|---|
| "verify a file hasn't been altered" | Hashing |
| "protect a password from precomputed cracking tables" | Salting |
| "encrypt a message so only the intended recipient can read it, without pre-sharing a key" | Asymmetric encryption |
| "encrypt large volumes of data efficiently between parties who already share a key" | Symmetric encryption |
| "prove a document came from a specific sender and wasn't altered" | Digital signature |
| "hide a secret message inside an image file" | Steganography |
| "replace credit card numbers with non-sensitive substitutes in a database" | Tokenization |
| "deliberately slow down password hashing to resist brute force" | Key stretching |
| "store cryptographic keys in dedicated, tamper-resistant hardware at scale" | HSM |
| "a certificate needs to be checked for revocation in real time" | OCSP |

---

## 🧠 Why This Pattern Works

Each cryptographic tool solves exactly one problem: hashing proves integrity (not confidentiality — it's one-way), encryption (symmetric or asymmetric) provides confidentiality, digital signatures combine integrity and non-repudiation, and obfuscation techniques (masking, tokenization, steganography) hide or substitute data without necessarily using traditional key-based encryption at all. Matching the scenario's actual goal — "prove," "hide," "protect," or "verify" — to one of these categories resolves most questions.

---

## 🚨 Common Mistakes

- Recommending encryption when the actual need is integrity verification — that's hashing, and encryption doesn't inherently prove nothing was altered
- Confusing tokenization (replaces data with a substitute, often reversible via a lookup) with hashing (one-way, not meant to be reversed)
- Picking symmetric encryption for a scenario emphasizing secure key exchange with no prior shared secret — that's exactly what asymmetric encryption solves
- Forgetting salting defends against rainbow tables specifically, while key stretching defends against brute-force speed — they're complementary, not interchangeable

---

## 💡 Wisdom from Mímir

Ask "does this need to be reversed later, or just verified?" Reversible and confidential → encryption. Verifiable but never reversed → hashing.

---

## 🔗 Related Notes

- [[Change Management and Cryptographic Solutions]] — full concept writeup
- [[Security+ Codex]]
