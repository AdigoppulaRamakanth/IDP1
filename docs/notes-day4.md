# Day 4 - Certificate authority (AD CS) and its weaknesses

## What I built
- **CA01**: cloned from the template, static IP 10.10.10.11, joined corp.lab, placed in OU=Servers,OU=Tier0 (a CA is a Tier 0 asset because it can issue logon certificates for any user).
- **CORP-ROOT-CA**: an Enterprise Root CA, RSA 4096, SHA-256, 10-year validity, trusted automatically by the whole domain.
- **CA auditing**: AuditFilter 127 (all 7 categories), template-load auditing, Certification Services audit subcategory on, Security log raised to 1 GB.

## Planted certificate weaknesses (for the Day 12 audit)
| ID | Name | Misconfiguration | Impact |
|---|---|---|---|
| W8 | ESC1 | Template allows enrollee-supplied subject + low-priv enroll + client auth, no approval | A normal user can request a cert as any user, including a Domain Admin |
| W9 | ESC4 | Low-priv group has Full Control over the template | They can turn a safe template into an ESC1 one |
| W10 | ESC8 | HTTP web enrollment enabled, accepts NTLM | NTLM relay of a machine account to get a cert as it |

Isolated lab only. Exploit steps are not published; only the misconfigurations are named.

## Privilege handling
- Gave adm-t0-sai Enterprise Admins only for the CA install, then removed it (just-in-time privilege). Leaving it would have been a standing weakness.

## What I learned
- An Enterprise CA install writes to the forest Configuration partition, so it needs Enterprise Admins, not just Domain Admins. Running it as the local Administrator fails with a range-constraint error.
- CA01 did not have the AD PowerShell module or dsacls by default; those come with the AD DS / RSAT tools. AD object commands and dsacls were run on DC01, CA cmdlets on CA01.
- Creating a certificate template purely with New-ADObject is unreliable because templates have many mandatory attributes. Duplicating an existing template in certtmpl.msc is the dependable method.
- A certificate behaves like a long-lived credential, so CA auditing and template-change logging matter as much as the CA itself.

## Snapshots
- day4-ca-clean: CA installed, no weaknesses
- day4-complete: end of Day 4
