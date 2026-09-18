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
  - A+ System Assembly
  - Motherboard CPU Power Supply Printers
publish: true
permalink: comptia-a-core-1-220-1201/system-assembly-power-and-printers
---

# <span class="rune">ᛟ</span> System Assembly, Power, and Printers

> *Motherboards, power supplies, and printers all fail the same exam question: know the connector, the setting, and the maintenance step.*

---

## 🎯 Purpose

The second half of the 25%-weighted Hardware domain (3.5-3.8): installing/configuring motherboards, CPUs, and add-on cards; installing the correct power supply; and deploying/maintaining printers. Builds on component knowledge from [[Core Hardware Components]].

---

## 🧠 Key Ideas

- Motherboard **form factor** (ATX, microATX, ITX — largest to smallest) must match the case; **CPU socket type** must match the CPU vendor and generation
- Firmware settings live in BIOS/UEFI: boot options, security features (TPM, Secure Boot, boot/BIOS passwords), and hardware monitoring
- A power supply is chosen by **wattage**, **efficiency rating**, and **connector compatibility** — not just "does it fit"
- Printer deployment is driver + connectivity + configuration; printer *maintenance* is a completely different, printer-technology-specific skill set
- Each printer technology (laser, inkjet, thermal, impact) has its own predictable failure points and maintenance routine

---

## ⚙️ How It Works

### Motherboard form factors and compatibility

| Form factor | Size | Note |
|---|---|---|
| ATX | Largest | Most expansion slots |
| microATX | Mid | Fewer slots, smaller case |
| ITX | Smallest | Minimal expansion, small-form-factor builds |

Compatibility checklist: **CPU socket type** (AMD vs. Intel, generation-specific) must match the CPU; **RAM type/generation** must match the board; **power connector** (20+4 pin) must match the PSU.

### BIOS/UEFI settings

| Setting | Purpose |
|---|---|
| Boot options | Boot device order |
| Secure Boot | Only allows signed/trusted OS bootloaders to run |
| TPM | Hardware-backed encryption key storage (required for BitLocker) |
| BIOS/Boot password | Prevents unauthorized firmware changes or booting |
| Temperature/fan monitoring | Hardware health checks at the firmware level |
| Virtualization support | Must be enabled in firmware for hypervisors to use hardware acceleration |

### Expansion cards and cooling

Expansion cards: sound card, video card, capture card, NIC. Cooling: fans, heat sink, thermal paste/pads, liquid cooling — thermal paste/pads exist to eliminate microscopic air gaps between the CPU and heat sink.

### Power supply selection

| Factor | What to check |
|---|---|
| Input voltage | 110-120VAC vs. 220-240VAC (region-dependent) |
| Output rails | 3.3V, 5V, 12V — different components draw from different rails |
| Wattage rating | Must exceed total system draw with headroom |
| Modular vs. non-modular | Modular = only attach cables you need, cleaner build |
| Energy efficiency | 80 PLUS ratings (Bronze/Gold/Platinum, etc.) |
| Redundant PSU | Servers — a second PSU takes over on failure |

### Printer deployment

1. Unbox and choose a setup location.
2. Install the appropriate **driver** for the OS (PCL vs. PostScript language).
3. Connect via USB, Ethernet, or wireless.
4. Configure settings: duplex, orientation, tray settings, quality.
5. Set up security: user authentication, badging, audit logs, secured prints.
6. Configure network scan services (email, SMB, cloud) as needed.

### Printer maintenance by technology

| Technology | Maintenance |
|---|---|
| Laser | Replace toner, apply maintenance kit, calibrate, clean |
| Inkjet | Clean printheads, replace cartridges, calibrate, clear jams |
| Thermal | Replace paper, clean heating element, remove debris |
| Impact | Replace ribbon, printhead, and paper |

---

## 💻 Examples

```text
"A new CPU won't POST in a motherboard with the correct socket type."
→ Check BIOS/UEFI compatibility — may need a firmware update for a newer CPU

"A server needs to survive a power supply failure without going down."
→ Redundant power supply

"A laser printer produces faded prints across the whole page."
→ Toner is low or unevenly distributed — replace/redistribute toner

"A thermal receipt printer produces blank output despite loading paper
correctly."
→ Check the heating element and confirm special thermal paper is loaded
(thermal paper is heat-reactive, not ink-based)

"A workstation needs BitLocker enabled but the option is greyed out."
→ TPM is likely disabled in BIOS/UEFI
```

---

## 🚨 Common Mistakes

- Assuming any 20+4 pin PSU works with any board — always confirm total wattage headroom, not just connector fit
- Treating printer maintenance as one universal routine — laser, inkjet, thermal, and impact each have distinct failure points
- Forgetting TPM must be enabled in firmware *before* BitLocker will work
- Mixing up PCL and PostScript as interchangeable — they're different print description languages with different driver requirements

---

## 📖 Further Reading

- [CompTIA A+ Core 1 (220-1201) official exam objectives](https://www.comptia.org/certifications/a)

---

## 🔗 Related Notes

- [[Core Hardware Components]]
- [[Selecting the Right Power Supply for a Build]] — drill note
- [[Choosing a Multifunction Printer Connection Type]] — drill note
- [[Troubleshooting Printer Issues]] — drill note
- [[A+ Core 1 Codex]]
