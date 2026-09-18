---
type: cheat-sheet
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
tags:
  - security-plus
  - cheat-sheet
aliases:
  - Security+ Quick Reference
publish: true
permalink: comptia-security-sy0-701/security-cheat-sheet
---

# <span class="rune">ᛊ</span> Security+ Cheat Sheet

> *Last-mile reference. If you can fill in every row of this from memory, you're ready.*

---

## Control categories vs. types

Category: Technical, Managerial, Operational, Physical · Type: Preventive, Deterrent, Detective, Corrective, Compensating, Directive

## Fundamentals

CIA triad: Confidentiality, Integrity, Availability · AAA: Authentication → Authorization → Accounting · Non-repudiation = can't deny an action

## Zero Trust

Control plane (decides): Policy Engine, Policy Administrator, adaptive identity · Data plane (enforces): Policy Enforcement Point, implicit trust zones

## Cryptography

Symmetric (one key, fast) vs. Asymmetric (public/private pair, solves distribution) · Hashing = integrity, one-way, NOT encryption · Salting defeats rainbow tables · Key stretching slows brute force · Self-signed = no CA, untrusted publicly

## Threat actors

Nation-state (espionage/war) · Organized crime (financial) · Hacktivist (political) · Insider threat (access+motive) · Unskilled attacker (low skill) · Shadow IT (unauthorized, not malicious)

## Social engineering

Phishing (email) · Vishing (voice) · Smishing (SMS) · Spear phishing (targeted person) · Whaling (executive) · Pretexting · Watering hole · Typosquatting

## Vulnerability types

Race condition (TOC/TOU) · VM escape (virtualization) · Zero-day (no patch exists) · Misconfiguration · Side loading/jailbreaking (mobile)

## Password attacks

Spraying = few passwords, many accounts (avoids lockout) · Brute force = many passwords, one account (risks lockout)

## DDoS

Amplified = small request, huge response · Reflected = spoofed source address redirects responses

## Mitigation techniques

Segmentation (limits spread, stays connected) vs. Isolation (fully cuts off) · Least privilege · Application allow list · Hardening = disable unused ports/services, change defaults

## Architecture

Air-gapped = zero connectivity · Logical segmentation = network-based isolation · Fail-open (availability) vs. Fail-closed (security) · Inline (can block) vs. Tap/monitor (observe only) → IPS vs. IDS

## Data protection

States: at rest, in transit, in use · Data sovereignty = legal jurisdiction by location · Masking/Tokenization = obscure/substitute

## Resilience

Hot > Warm > Cold site (readiness & cost) · Active-active (all serve) vs. Active-passive (standby) · Load balancing (distribute) vs. Clustering (failover)

## Mobile deployment models

BYOD (employee-owned) · COPE (corporate-owned, personal use ok) · CYOD (employee picks from approved list, corporate-owned)

## Vulnerability management cycle

Identify → Analyze (confirm real vs. false positive/negative, CVSS score) → Respond (patch/segment/compensate/except) → Validate (rescan/verify)

CVE = identifies a vulnerability · CVSS = scores its severity · False negative (missed, dangerous) worse than false positive (wasted effort)

## Email security (anti-spoofing)

SPF = authorized senders · DKIM = message signature/integrity · DMARC = enforcement policy for SPF/DKIM failures + reporting

## Access control models

DAC (owner decides) · MAC (system/classification decides) · RBAC (role decides) · Rule-based (condition decides) · ABAC (multiple attributes decide)

## MFA factors

Know (password) · Have (token/key) · Are (biometric) · Somewhere you are (geolocation) — true MFA = different categories

## Incident response (7 steps, in order)

Preparation → Detection → Analysis → Containment → Eradication → Recovery → Lessons learned

## Digital forensics

Chain of custody · Legal hold · Order of volatility · Acquisition · Preservation · E-discovery

## Governance hierarchy

Guidelines (optional) → Policies (mandatory, general) → Procedures (mandatory, steps) → Standards (mandatory, specific)

## Data roles

Owner (accountable) · Controller (decides how/why) · Processor (acts on controller's behalf) · Custodian/Steward (day-to-day technical mgmt)

## Risk formula

SLE × ARO = ALE (Single Loss Expectancy × Annualized Rate of Occurrence = Annualized Loss Expectancy)

## Risk strategies

Transfer (insurance) · Accept (exemption/exception) · Avoid (eliminate activity) · Mitigate (reduce likelihood/impact)

## Business impact metrics

RTO = max downtime · RPO = max data loss · MTTR = time to repair · MTBF = time between failures

## Third-party agreements

MSA (overarching terms) · SOW/WO (specific work) · NDA (confidentiality) · SLA (performance) · MOU (informal understanding) · MOA (more formal agreement) · BPA (partnership terms)

## Audits & pen testing

Internal (self/compliance) vs. External (regulatory/independent) · Known/Partially known/Unknown environment · Passive (no contact) vs. Active (direct contact) reconnaissance

---

## 🔗 Related Notes

- [[Security+ Codex]]
