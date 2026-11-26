# Day 3 - Building a realistic company in corp.lab

## What I built
- **OU structure:** Tier0 and Tier1 (Admins, Servers), Employees (Finance, HR, IT, Sales), Groups, ServiceAccounts, Workstations, Disabled. Top-level OUs protected from accidental deletion.
- **Tiered admin accounts:** `adm-t0-sai` (Domain Admins), `adm-t1-sai` (Tier 1 server admins), `sai` (daily account, no admin rights). Each has its own password.
- **Groups (AGDLP):** GG-Finance, GG-HR, GG-IT, GG-Sales, GG-T1-ServerAdmins, and DL-FinanceShare-RW (contains GG-Finance).
- **10 employees** with department, title, employee ID, manager and office, as a source for HR-driven provisioning and access reviews later.
- **leaver.test** (Sales, E1099): a dedicated account for the end-to-end leaver timing test.

## Planted weaknesses (for the Day 12 attack audit)
| ID | Weakness | Target |
|---|---|---|
| W1 | Service account with SPN and weak password (Kerberoasting) | svc-sql |
| W2 | Kerberos pre-authentication disabled (AS-REP roasting) | hugo.mercier |
| W3 | Password stored in account description | thomas.petit |
| W4 | Helpdesk group has GenericAll on the Tier 1 Admins OU | GG-IT |
| W5 | Forgotten contractor account in Domain Admins | adm-old-contractor |
| W6 | No account lockout (threshold 0) plus a guessable password | domain policy / manon.lefebvre |
| W7 | Replication rights (DCSync) on a backup account | svc-backup |

Passwords are weak, from common wordlist patterns. They are not published here.

## Evidence for the leaver test
- Advanced auditing enabled on DC01: User Account Management, Security Group Management, Kerberos Authentication Service, Kerberos Service Ticket Operations, Credential Validation, Logon (Success and Failure). Security log raised to 1 GB.
- Time: DC01 (PDC emulator) synced to external NTP (fr.pool.ntp.org / time.windows.com), so timestamps can be compared with Entra ID logs.
- Baseline: leaver.test credential check returned `True`, logged as event 4776 (NTLM credential validation). An interactive sign-in will log 4768 (Kerberos).

## What I learned
- The domain password policy rejects passwords that contain the username or parts of the display name, not only short or simple passwords.
- `New-ADUser` can leave a half-created, disabled account when the password is rejected. Scripts need `-ErrorAction Stop` and try/catch, or they report false success.
- PowerShell merges several outputs into one table. Use `Format-Table` per command when checking results.
- Members of protected groups (Domain Admins) are covered by AdminSDHolder, which resets their permissions every hour. That's why W4 targets Tier 1 admins.
- A control test needs a before and an after: prove the leaver can sign in before testing that they can't.

## Snapshots
- `day3-before-weaknesses`: clean structure, no weaknesses
- `day3-complete`: end of Day 3
