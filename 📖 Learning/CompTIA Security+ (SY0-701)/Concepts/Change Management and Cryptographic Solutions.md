---
type: concept
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
  - Security+ Cryptography
  - Security+ Change Management
publish: true
permalink: comptia-security-sy0-701/change-management-and-cryptographic-solutions
---

# <span class="rune">ᛟ</span> Change Management and Cryptographic Solutions

> *A change no one reviewed is a vulnerability with a deployment date. Cryptography without key management is just obfuscation with extra steps.*

---

## 🎯 Purpose

Covers 1.3-1.4 of the General Security Concepts domain: change management's security impact, and appropriate cryptographic solutions.

---

## 🧠 Key Ideas

- Change management is a security control in itself — an unreviewed change (no impact analysis, no backout plan, no testing) is one of the most common causes of real-world security incidents
- **PKI** provides trust through key pairs: the **public key** encrypts/verifies, the **private key** decrypts/signs — and both roles never do each other's job
- **Symmetric** encryption uses one shared key (fast, hard to distribute securely); **asymmetric** uses a public/private key pair (slower, solves the distribution problem)
- **Hashing** (one-way, verifies integrity) is not encryption (two-way, protects confidentiality) — **salting** defends hashes specifically against precomputed rainbow-table attacks
- Certificates need a **root of trust** — a CA-issued certificate is trusted because the CA itself is trusted; a **self-signed** certificate skips that trust chain entirely

---

## ⚙️ How It Works

### Change management's security dimensions

| Element | Purpose |
|---|---|
| Approval process, ownership, stakeholders | Ensures accountability before a change happens |
| Impact analysis, test results | Predicts and verifies consequences before deployment |
| Backout plan | Defines how to undo the change if it fails |
| Maintenance window | Controls *when* risk is introduced |
| Allow/deny lists, restricted activities | Technical guardrails during the change |
| Documentation, version control | Keeps diagrams/policies/procedures accurate after the change |

### PKI and certificates

| Term | Role |
|---|---|
| Public key | Encrypts data / verifies a signature — can be shared freely |
| Private key | Decrypts data / creates a signature — must stay secret |
| Key escrow | A trusted third party holds a copy of keys for recovery |
| CA (Certificate Authority) | Issues and vouches for certificates |
| CRL / OCSP | Ways to check if a certificate has been revoked |
| Self-signed | No CA involved — fine internally, untrusted publicly |
| Root of trust | The foundational trusted anchor a certificate chain builds from |
| CSR | The request submitted to a CA to obtain a certificate |
| Wildcard certificate | Covers all subdomains of a domain with one certificate |

### Encryption types and scope

| Type | Detail |
|---|---|
| Symmetric | One shared key — fast, key distribution is the challenge |
| Asymmetric | Public/private key pair — slower, solves distribution |
| Levels | Full-disk, partition, file, volume, database, record — each protects a different scope |
| Transport/communication | Protects data moving across a network (e.g., TLS) |

### Hashing, obfuscation, and integrity

| Concept | Purpose |
|---|---|
| Hashing | One-way function verifying integrity — not reversible, not encryption |
| Salting | Random data added before hashing to defeat precomputed/rainbow-table attacks |
| Key stretching | Deliberately slows down hashing to resist brute force |
| Digital signature | Proves authenticity and integrity using a private key |
| Steganography | Hides data within other data (e.g., inside an image) |
| Tokenization | Replaces sensitive data with a non-sensitive substitute token |
| Data masking | Obscures specific data elements while preserving format |
| Blockchain | An open, distributed public ledger providing tamper-evidence |

### Cryptographic hardware

| Tool | Purpose |
|---|---|
| TPM | Hardware-based key storage on a motherboard |
| HSM | Dedicated hardware for managing cryptographic keys at scale |
| Secure enclave | Isolated, protected processing area for sensitive operations |

---

## 💻 Examples

```text
"A proposed firewall rule change is deployed without documenting how to
reverse it if something breaks."
→ Missing backout plan — a change management failure

"A message is encrypted with the recipient's public key so only their
private key can decrypt it."
→ Asymmetric encryption

"A password is hashed, but an attacker precomputes hash values for common
passwords to crack it instantly."
→ Missing salt — the defense against this exact attack

"A certificate is issued for an internal-only test server without
involving a public CA."
→ Self-signed certificate

"An organization needs to prove a signed document hasn't been altered
and definitively came from a specific sender."
→ Digital signature (integrity + non-repudiation)
```

---

## 🚨 Common Mistakes

- Confusing hashing (one-way, integrity) with encryption (two-way, confidentiality) — a hash can never be "decrypted" back to the original
- Assuming symmetric encryption is "less secure" than asymmetric — it's actually faster and equally strong; the trade-off is key distribution, not strength
- Forgetting salting specifically defeats rainbow tables — it doesn't slow down brute force the way key stretching does
- Treating a self-signed certificate as equivalent to a CA-issued one for public-facing use — it will trigger trust warnings without a recognized root of trust

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Security Controls and Fundamental Concepts]]
- [[Evaluating Change Management Impact on Security]] — drill note
- [[Choosing the Right Cryptographic Solution]] — drill note
- [[Security+ Codex]]
