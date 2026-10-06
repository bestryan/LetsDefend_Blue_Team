# SOC246: Forced Authentication Detected

|                     |                                                              |
| ------------------- | ------------------------------------------------------------ |
| **Platform**        | LetsDefend                                                   |
| **Severity**        | _(not provided — confirm from alert)_                        |
| **Verdict**         | True Positive                                                |
| **Affected device** | 104.26.15[.]61 (Cisco Catalyst 8000v, Cisco IOS XE 17.12.1a) |

---

## Summary (What happened)

On 12th Dec 2023, an alert was triggered because multiple POST requests were seen in quick succession from the same IP to the fixed URI `/accounts/login`. The source IP `120.48.36[.]175` was marked malicious in a reputation check. Proxy logs showed multiple POST login attempts directed at `104.26.15[.]61`, the organisation's Cisco virtual router backdoor interface. The pattern matched a dictionary attack, and the last attempt resulted in a successful logon.

## Timeline (When it happened — AEST)

| Time                  | Event                                                               |
| --------------------- | ------------------------------------------------------------------- |
| 12 Dec 2023, 21:50:44 | Brute-force/dictionary attack begins (11 attempts recorded in logs) |
| 12 Dec 2023, 22:15:48 | Attacker successfully logs in using the default admin account       |

## Artifacts (Indicators of Compromise)

| Type               | Value             |
| ------------------ | ----------------- |
| Threat actor IP    | `120.48.36[.]175` |
| Targeted device IP | `104.26.15[.]61`  |

## MITRE ATT&CK Mapping

| Technique                        | ID        |
| -------------------------------- | --------- |
| Brute Force: Dictionary Attack   | T1110.001 |
| Valid Accounts: Default Accounts | T1078.001 |
| Network Device CLI               | T1059.008 |

## Root Cause (Why it happened)

The device `104.26.15[.]61` was exposed to a dictionary attack against its login page. The device was still using default admin account credentials, which the attacker successfully guessed, resulting in a successful unauthorized logon.

## Impact (Who/What was affected)

Endpoint device `104.26.15[.]61` is impacted — a Cisco Catalyst 8000v running Cisco IOS XE 17.12.1a.

## Action Taken (How it was fixed)

- Isolated the device `104.26.15[.]61` using EDR containment to prevent further data leakage.

## Recommendations (Future prevention)

1. Block the attacker's IP globally.
2. Reset compromised user credentials, and change the default admin account credentials immediately.
3. Enforce account lockout and MFA.
