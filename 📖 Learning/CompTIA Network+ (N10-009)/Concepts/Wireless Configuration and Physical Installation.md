---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA Network+
difficulty: Intermediate
tags:
  - network-plus
  - network-implementation
  - wireless
aliases:
  - Network+ Wireless Configuration
  - Network+ Physical Installation
publish: true
permalink: comptia-network-n10-009/wireless-configuration-and-physical-installation
---

# <span class="rune">ᛟ</span> Wireless Configuration and Physical Installation

> *A wireless network is only as good as its channel plan, and a data center is only as reliable as its power and cooling.*

---

## 🎯 Purpose

Covers 2.3-2.4 of the Network Implementation domain: selecting/configuring wireless devices and technologies, and physical installation factors (racks, cabling, power, environment).

---

## 🧠 Key Ideas

- Wireless configuration layers channel/frequency selection, network type (infrastructure vs. mesh vs. ad hoc vs. point-to-point), encryption, and authentication (PSK vs. Enterprise)
- **BSSID** identifies one specific access point's radio; **SSID/ESSID** is the network name that can span many APs acting as one logical network
- **PSK** authentication uses one shared password for everyone; **Enterprise** authentication (802.1X-based) authenticates each user/device individually against a directory
- Physical installation planning covers **space** (rack size, IDF/MDF locations), **cabling** (patch panels, fiber distribution), **power** (UPS, PDU, voltage), and **environment** (humidity, temperature, fire suppression)

---

## ⚙️ How It Works

### Wireless configuration factors

| Factor | Options |
|---|---|
| Frequency | 2.4GHz (range), 5GHz (speed), 6GHz (newest, least congested) |
| Channels | Width, non-overlapping channel selection, regulatory limits |
| Network type | Infrastructure (AP-based), mesh, ad hoc (peer-to-peer), point-to-point |
| Identifiers | BSSID (one AP's radio), SSID/ESSID (the network name across APs) |
| Encryption | WPA2, WPA3 |
| Authentication | PSK (shared password) vs. Enterprise (802.1X, per-user via RADIUS) |
| Antennas | Omnidirectional (coverage in all directions) vs. directional (focused, longer range one way) |
| AP management | Autonomous (self-managed) vs. lightweight (centrally managed by a controller) |
| Guest access | Captive portal-based guest networks, isolated from the main network |

**Band steering** pushes capable clients to the less congested 5GHz/6GHz band automatically.

### Physical installation

| Category | Considerations |
|---|---|
| Space | Rack size, IDF (intermediate distribution frame) vs. MDF (main distribution frame), port-side exhaust/intake airflow direction |
| Cabling | Patch panels, fiber distribution panels, lockable enclosures |
| Power | UPS (battery backup), PDU (distributes power within a rack), power load, voltage |
| Environment | Humidity, temperature, fire suppression |

---

## 💻 Examples

```text
"A large office needs one wireless network name to work seamlessly as a
user walks between multiple access points."
→ ESSID (extended service set spanning multiple APs)

"A corporate wireless network needs every employee to authenticate with
their own individual directory credentials, not a shared password."
→ Enterprise authentication (802.1X)

"A wireless deployment in a dense apartment building needs the frequency
band least likely to be congested by neighbors."
→ 6GHz

"A server room's airflow design pulls cool air in the front and exhausts
hot air out the back."
→ Port-side exhaust/intake consideration in rack planning

"Network equipment must stay powered through a brief outage long enough
for generators to start."
→ UPS
```

---

## 🚨 Common Mistakes

- Confusing BSSID (one AP's identifier) with SSID/ESSID (the network name, which can span many APs)
- Assuming PSK and Enterprise authentication offer the same security level — Enterprise provides per-user accountability and revocation that PSK cannot
- Overlooking environmental factors (humidity, fire suppression) as "not really networking" — they're explicitly tested physical installation factors
- Treating a UPS and a PDU as the same thing — a UPS provides backup power, a PDU just distributes power within a rack

---

## 📖 Further Reading

- [CompTIA Network+ (N10-009) official exam objectives](https://www.comptia.org/certifications/network)

---

## 🔗 Related Notes

- [[Routing and Switching Configuration]]
- [[Configuring Wireless Devices for a Scenario]] — drill note
- [[Planning a Physical Network Installation]] — drill note
- [[Network+ Codex]]
