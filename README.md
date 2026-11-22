# Hybrid Identity Security Lab

A home lab that simulates a company's identity platform: on-prem Active Directory synced to Microsoft Entra ID.

## What this lab shows
- HR-driven joiner / mover / leaver (CSV -> Entra inbound provisioning -> AD -> Entra)
- Access reviews and Privileged Identity Management (PIM)
- Passwordless sign-in (Temporary Access Pass, passkeys, Windows Hello)
- AD and AD CS attack audit (ESC1, ESC4, ESC8), fixes, and detections in Wazuh

## Lab
| VM | Role |
| --- | --- |
| DC01 | Domain controller (corp.lab) |
| CA01 | Certificate Authority |
| WS01 / WS02 | Windows 11 clients |
| KALI01 | Attacker / audit |
| WAZUH01 | SIEM |

Host: Fedora + KVM (virt-manager), 16 GB RAM.

## Status
- [x] Host and lab network ready
- [x] Day 1: Windows Server 2022 template (updated, sysprepped)
- [x] Day 1: lab network + Windows Server 2022 template (updated, sysprepped)
- [ ] Day 2: DC01 and domain corp.lab
