# RDP Brute Force Detected

|                   |                           |
| ----------------- | ------------------------- |
| **Platform**      | LetsDefend                |
| **Severity**      | Medium                    |
| **Verdict**       | True Positive             |
| **Affected host** | Matthew (172.16.17[.]148) |

---

## Summary (What happened)

On 7th Mar 2024, an alert was triggered by "RDP Brute Force Detected" against host Matthew (172.16.17[.]148). Login failures from a single source using different, non-existent account names were flagged. Source IP `218.92.0[.]56` and the volume of failed logons across different usernames on RDP port 3389 were assessed as suspicious. The final login attempt succeeded, and activity was subsequently discovered on the host's terminal.

## Timeline (When it happened)

| Time                  | Event                                                                    |
| --------------------- | ------------------------------------------------------------------------ |
| 7 Mar/Sep 2024, 11:44 | Attacker began RDP login attempts against host Matthew (172.16.17[.]148) |
| 7 Mar 2024, 11:45:51  | `whoami` executed on host                                                |
| 7 Mar 2024, 11:45:58  | `net user letsdefend` executed                                           |
| 7 Mar 2024, 11:46:34  | `net localgroup administrators` executed                                 |
| 7 Mar 2024, 11:46:53  | `netstat -ano` executed                                                  |

> **Note:** the summary states 7th **Mar** 2024, but the timeline states 7th **Sep** 2024. Confirm the correct month against the alert before finalizing.

## Artifacts (Indicators of Compromise)

| Type         | Value                                                               |
| ------------ | ------------------------------------------------------------------- |
| IP address   | `218.92.0[.]56` — VirusTotal flagged as malicious (SSH/brute-force) |
| Event volume | ~30 events recorded across firewall, proxy and OS logs              |

**Suspicious terminal executions (post-compromise discovery activity):**

```
Mar 7 2024 11:45:51 whoami
Mar 7 2024 11:45:58 net user letsdefend
Mar 7 2024 11:46:34 net localgroup administrators
Mar 7 2024 11:46:53 netstat -ano
```

These commands identify the current user, check a specific local account, enumerate the local administrators group, and list active network connections — consistent with initial post-access discovery.

## MITRE ATT&CK Mapping

| Technique                                 | ID        |
| ----------------------------------------- | --------- |
| Brute Force                               | T1110     |
| Remote Desktop Protocol                   | T1021.001 |
| System Owner/User Discovery               | T1033     |
| Permission Groups Discovery: Local Groups | T1069.001 |
| System Network Connections Discovery      | T1049     |

## Root Cause (Why it happened)

RDP was exposed to the internet, allowing the attacker to brute-force host Matthew from an external IP. The final login attempt succeeded. The environment did not enforce account lockout or MFA, which would otherwise have limited the number of attempts.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Matthew's device is compromised.
- Matthew's credentials are compromised.
- Any admin account or network (subnet) information discovered during the attacker's enumeration may also have been exposed.

## Action Taken (How it was fixed)

- Isolated host Matthew (172.16.17[.]148) using EDR containment.

In a real-world scenario, this would be followed by:

- Deep searching logs to identify unusual activity on other hosts associated with this incident.
- Investigating for lateral movement and unauthorised access.

## Recommendations (Future prevention)

1. Block the attacker's IP at the firewall or proxy.
2. Reimage the host.
3. Rotate credentials for the affected account.
4. Remove internet exposure of RDP. If it must be reachable, use a VPN or bastion host.
5. Enforce account lockout and MFA.
