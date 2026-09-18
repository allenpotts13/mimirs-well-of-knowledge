---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Security+
difficulty: Beginner
tags:
  - security-plus
  - threats-vulnerabilities
aliases:
  - Security+ Threat Actors
  - Security+ Attack Vectors
publish: true
permalink: comptia-security-sy0-701/threat-actors-and-attack-vectors
---

# <span class="rune">ᛟ</span> Threat Actors and Attack Vectors

> *Motivation names the actor. Delivery channel names the vector. Neither one alone tells the whole story.*

---

## 🎯 Purpose

Covers 2.1-2.2 of the 22%-weighted Threats, Vulnerabilities, and Mitigations domain — the second-largest domain on the exam: threat actor types/motivations, and common threat vectors/attack surfaces.

---

## 🧠 Key Ideas

- Threat actors are classified by **who they are** (nation-state, hacktivist, insider, organized crime, unskilled attacker, shadow IT) and separately by **attributes** (internal/external, resources/funding, sophistication)
- **Motivation** explains *why* — data exfiltration, espionage, financial gain, revenge, disruption, philosophical/political belief, war — and different actor types tend toward different motivations
- **Shadow IT** is a threat actor category worth remembering precisely: it's not malicious outsiders, it's *employees* deploying unauthorized tech that bypasses security review
- Attack surfaces split into **message-based** (email/SMS/IM), **image/file-based**, **voice**, **removable device**, **network-based** (unsecure wireless/wired/Bluetooth), and **human/social engineering** vectors — each requiring different defenses
- **Supply chain** as a vector means the compromise doesn't target you directly — it targets your MSP, vendor, or supplier first

---

## ⚙️ How It Works

### Threat actor types and typical profile

| Actor | Typical resources | Typical motivation |
|---|---|---|
| Nation-state | Very high | Espionage, war, disruption |
| Organized crime | High, well-funded | Financial gain |
| Hacktivist | Variable | Philosophical/political belief |
| Insider threat | Internal access (not necessarily technical skill) | Revenge, financial gain, blackmail |
| Unskilled attacker | Low sophistication | Disruption/chaos, ethical curiosity |
| Shadow IT | Internal, unauthorized | Not malicious — convenience/productivity, but still a risk |

**Attributes to classify any actor:** internal vs. external, resources/funding level, sophistication/capability level.

### Threat vectors and attack surfaces

| Category | Examples |
|---|---|
| Message-based | Email, SMS, instant messaging |
| Image-based / File-based | Malicious payloads embedded in media or documents |
| Voice call | Vishing |
| Removable device | USB drops, infected media |
| Vulnerable/unsupported software | Unpatched or end-of-life applications |
| Unsecure networks | Wireless, wired, Bluetooth |
| Open service ports / default credentials | Exposed, unhardened services |
| Supply chain | MSPs, vendors, suppliers — compromise upstream of the target |

### Human vectors / social engineering

| Technique | Description |
|---|---|
| Phishing / Vishing / Smishing | Email / voice / SMS-based deception |
| Pretexting | Fabricated scenario to extract information |
| Business email compromise | Impersonating a trusted executive/vendor via email |
| Watering hole | Compromising a site the target is known to visit |
| Typosquatting | Registering misspelled domains to catch mistyped URLs |
| Brand impersonation | Posing as a trusted brand |
| Misinformation/disinformation | Spreading false information to manipulate |

---

## 💻 Examples

```text
"An employee installs an unapproved cloud storage app to share files
faster, unaware it bypasses corporate DLP."
→ Shadow IT

"An attacker registers 'gooogle.com' hoping users mistype the real
domain."
→ Typosquatting

"A well-funded group conducts a long-term espionage campaign against a
government agency."
→ Nation-state actor, motivated by espionage

"A vendor's compromised software update silently delivers malware to all
of that vendor's customers."
→ Supply chain attack vector

"An attacker compromises a website known to be frequently visited by
employees of a specific target company."
→ Watering hole attack
```

---

## 🚨 Common Mistakes

- Assuming insider threats require advanced technical skill — access and motive matter more than sophistication
- Treating shadow IT as automatically malicious — it's usually well-intentioned but still a genuine security risk
- Confusing vishing (voice) with smishing (SMS) — same phishing goal, different channel
- Underestimating supply chain as an indirect vector — organizations often have strong perimeter defenses but weak vendor vetting

---

## 📖 Further Reading

- [CompTIA Security+ (SY0-701) official exam objectives](https://www.comptia.org/certifications/security)

---

## 🔗 Related Notes

- [[Identifying Vulnerability Types]]
- [[Identifying Threat Actor Types and Motivations]] — drill note
- [[Identifying Threat Vectors and Attack Surfaces]] — drill note
- [[Security+ Codex]]
