---
type: concept
status: published
created: 2026-09-17
updated: 2026-09-17
technology: CompTIA A+
difficulty: Beginner
tags:
  - a-plus-1201
  - hardware
aliases:
  - A+ Core Hardware
  - RAM Storage Cables Displays
publish: true
permalink: comptia-a-core-1-220-1201/core-hardware-components
---

# <span class="rune">ᛟ</span> Core Hardware Components

> *Before you assemble anything, you need to name every cable and connector on sight.*

---

## 🎯 Purpose

The first half of the 25%-weighted Hardware domain (3.1-3.4): displays, cable types/connectors, RAM characteristics, and storage devices. [[System Assembly, Power, and Printers]] covers the second half — actually putting components together.

---

## 🧠 Key Ideas

- Display *types* (LCD variants, OLED, Mini-LED) are a different axis from display *attributes* (pixel density, refresh rate, resolution, color gamut) — the exam tests both
- Cable identification is pure memorization: connector shape → cable family → typical use
- RAM characteristics (form factor, DDR generation, ECC vs. non-ECC, channel config) determine compatibility, not just capacity
- Storage splits into three real categories: spinning hard drives, solid-state drives, and removable/optical media — each with its own interface and form-factor vocabulary
- RAID levels trade off capacity, redundancy, and performance differently — know which is which

---

## ⚙️ How It Works

### Display types vs. attributes

| Types | Attributes |
|---|---|
| LCD: IPS, TN, VA | Pixel density |
| OLED | Refresh rate |
| Mini-LED | Screen resolution |
| Touch screen/digitizer, inverter | Color gamut |

**IPS** = best color accuracy, wider viewing angles. **TN** = fastest response time, worst viewing angles. **VA** = best contrast, middle ground on speed.

### Cable and connector families

| Category | Examples |
|---|---|
| Network cables | Copper (UTP/STP, plenum-rated), optical (single-mode/multimode), RJ45, coaxial (F-type) |
| Video cables | HDMI, DisplayPort, DVI, VGA, USB-C |
| Hard drive cables | SATA, eSATA |
| Peripheral cables | USB 2.0/3.0, Serial, Thunderbolt |
| Fiber connectors | ST, SC, LC |
| Legacy/misc | Molex, DB9, RJ11 |

### RAM characteristics

| Characteristic | Options |
|---|---|
| Form factor | DIMM (desktop), SODIMM (laptop) |
| DDR generation | Higher = faster, and generations are **not** cross-compatible |
| ECC vs. non-ECC | ECC detects/corrects memory errors — servers/workstations; non-ECC = consumer desktops |
| Channel configuration | Multiple matched sticks run in parallel for more bandwidth |

### Storage devices

| Type | Interface | Form factor |
|---|---|---|
| HDD | SATA, SAS, PCIe | 2.5", 3.5" |
| SSD | SATA, NVMe | M.2, mSATA |
| Removable | — | Flash drives, memory cards |
| Optical | — | CD/DVD/Blu-ray drives |

**HDD spindle speed** matters for performance (5400/7200/10000 RPM). **NVMe** (via PCIe lanes) is dramatically faster than SATA SSDs because it bypasses the older SATA command protocol entirely.

### RAID levels

| RAID | Description | Fault tolerance | Usable capacity |
|---|---|---|---|
| 0 | Striping | None — one drive fails, all data lost | 100% |
| 1 | Mirroring | Survives 1 drive failure | 50% |
| 5 | Striping + distributed parity | Survives 1 drive failure | (n-1)/n |
| 6 | Striping + double parity | Survives 2 drive failures | (n-2)/n |
| 10 | Mirrored + striped | Survives multiple failures (depends on layout) | 50% |

---

## 💻 Examples

```text
"A graphic designer needs the most color-accurate laptop display available."
→ IPS panel

"A budget gaming monitor needs the fastest possible response time and color
accuracy is a secondary concern."
→ TN panel

"A server needs memory that can detect and correct single-bit errors."
→ ECC RAM

"A workstation needs the fastest possible storage for video editing and has
an available M.2 slot."
→ NVMe SSD

"A small business wants redundancy so the array survives a single drive
failure, using exactly 2 drives."
→ RAID 1 (mirroring)
```

---

## 🚨 Common Mistakes

- Assuming any M.2 drive is NVMe — M.2 is a form factor, NVMe is a protocol; M.2 SATA drives exist too
- Mixing up RAID 5 (survives 1 failure) with RAID 6 (survives 2 failures) — the number doesn't scale intuitively with tolerance
- Treating DDR generations as interchangeable because they physically look similar — they are not cross-compatible
- Confusing SATA (drive interface) with SATA cable pinout — same name, remember it as one connected system

---

## 📖 Further Reading

- [CompTIA A+ Core 1 (220-1201) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[System Assembly, Power, and Printers]]
- [[Choosing the Right RAM for a Build]] — drill note
- [[Choosing the Right Storage Device]] — drill note
- [[A+ Core 1 Codex]]
