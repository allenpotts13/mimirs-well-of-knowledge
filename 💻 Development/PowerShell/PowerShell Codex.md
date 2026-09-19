---
publish: true
status: published
permalink: powershell
description: "PowerShell concepts and a task-oriented playbook for troubleshooting Active Directory, networking, Windows, hardware, and Microsoft 365 from the command line."
---

# <span class="rune">ᚦ</span> PowerShell

> "Every other tool tells you what's wrong. PowerShell lets you go find out."

---

## 📚 Concepts

Foundational theory — one idea per note.

### Language Fundamentals
- [[Objects in PowerShell]]
- [[The Pipeline]]
- [[Filtering and Comparison Operators]]
- [[Variables and Data Types]]
- [[Loops and Iteration]]
- [[Error Handling]]
- [[Functions]]
- [[Modules]]
- [[CIM and WMI]]
- [[Remoting]]

### Domain Reference
- [[Active Directory Cmdlets]]
- [[Computer and System Information Cmdlets]]
- [[Networking Cmdlets]]
- [[User and Session Cmdlets]]
- [[Process and Service Cmdlets]]
- [[Event Log Cmdlets]]
- [[Storage Cmdlets]]
- [[Software Inventory Cmdlets]]
- [[Hardware Inventory Cmdlets]]
- [[Registry Cmdlets]]
- [[PowerShell Security Fundamentals]]
- [[Microsoft 365 and Entra ID Cmdlets]]

---

## 🧩 Playbook — Troubleshooting Patterns

Task-oriented, one real scenario per note — organized by the area of a system each one touches.

### Active Directory
- [[Finding and Unlocking a Locked-Out AD Account]]
- [[Resetting a User's Password via PowerShell]]
- [[Finding All Members of an AD Group]]
- [[Finding Stale or Inactive AD Computer Accounts]]
- [[Bulk-Creating AD Users from a CSV]]

### Authentication
- [[Checking a User's Current Logon Session]]
- [[Testing Credentials Without Locking an Account]]
- [[Managing Local Administrator Group Membership]]
- [[Auditing Failed Login Attempts from the Security Log]]

### Networking
- [[Testing Connectivity to a Remote Host and Port]]
- [[Flushing and Re-Registering DNS]]
- [[Finding a Computer's IP Configuration and Adapter Details]]
- [[Tracing a Network Path Hop by Hop]]
- [[Resetting a Stuck Network Adapter]]

### Windows
- [[Finding and Killing a Frozen Process]]
- [[Restarting a Hung Windows Service]]
- [[Reading the Last N System Errors from the Event Log]]
- [[Checking Windows Update History and Pending Updates]]
- [[Rebooting a Remote Computer Safely]]

### Applications
- [[Finding Installed Software on a Machine]]
- [[Silently Uninstalling an Application]]
- [[Checking Which Version of an Application Is Installed]]
- [[Finding Which Process Is Locking a File]]

### Hardware
- [[Retrieving BIOS and System Information]]
- [[Checking Disk Health and SMART Status]]
- [[Finding Free Disk Space Across All Drives]]
- [[Listing Connected USB and Plug-and-Play Devices]]

### Performance
- [[Finding the Top CPU or Memory-Consuming Processes]]
- [[Monitoring Real-Time Resource Usage]]
- [[Checking System Uptime and Last Boot Time]]
- [[Identifying What's Filling Up a Disk]]

### Microsoft 365
- [[Connecting to Microsoft Graph or Exchange Online]]
- [[Finding a User's Mailbox and License Details]]
- [[Resetting an Entra ID User's Password]]
- [[Checking a User's Group and License Assignments in Entra ID]]

### SCCM
- [[Triggering a Machine Policy Retrieval and Evaluation Cycle]]
- [[Checking SCCM Client Health on a Remote Computer]]
- [[Forcing an Application Deployment to Install Immediately]]
- [[Finding a Device's SCCM Collection Membership]]

---

## ⚡ Cheat Sheet

- [[PowerShell Cheat Sheet]] — dense one-page reference, organized by troubleshooting target

---

## 🔗 Related Notes

- [[Git Codex]]
- [[SQL Codex]]
- [[A+ Core 2 Codex]] — Windows OS troubleshooting fundamentals
- [[Network+ Codex]] — networking concepts underlying the Networking playbook
