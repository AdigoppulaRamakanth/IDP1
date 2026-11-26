# Leaver workflow - end-to-end timing test

Goal: measure when each step of the leaver process happens, and the delay between systems.
Test account: `leaver.test` (Sales, employee ID E1099)

## Baseline (before)
| Check | Result | Time | Evidence |
|---|---|---|---|
| Credential check on DC01 | Success | 2026-11-26 19:04:12 | Event 4776 |

## Timeline (after the leaver event)
| Step | Event | Time | Evidence | Delay from T0 |
|---|---|---|---|---|
| T0 | HR marks employee as left | | HR file | 0 |
| T1 | AD account disabled | | Event 4725 on DC01 | |
| T2 | Disable synced to Entra ID | | Cloud Sync provisioning log | |
| T3 | On-prem sign-in denied | | Event 4768 failure on DC01 | |
| T4 | Cloud sign-in denied | | Entra sign-in log (error 50057) | |
| T5 | Already-open session still works? | | Manual test | |

## Result
To be completed.
