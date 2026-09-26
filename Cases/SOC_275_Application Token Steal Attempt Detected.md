# SOC275: Application Token Steal Attempt Detected

|                        |                            |
| ---------------------- | -------------------------- |
| **Platform**           | LetsDefend                 |
| **Severity**           | Medium                     |
| **Verdict**            | True Positive              |
| **Affected user/host** | Gloriana (172.16.17[.]172) |
| **Impersonated brand** | Discord                    |

---

## Summary (What happened)

On 19th Apr 2024, an alert was triggered by a `GET /reset-password?email=gloriana@letsdefend.io HTTP/1.1` request. Investigation found that the victim, Gloriana (172.16.17[.]172), received a phishing email impersonating Discord, prompting a password reset. The embedded link was designed to redirect to an attacker-controlled site, `homespottersf[.]com`, when clicked. Evidence indicates the user may have completed the password reset flow and submitted their old password on the malicious page. Following this, the attacker was found to have obtained a reset token via `POST /reset-password?token=123letsdefendisthebest123 HTTP/1.1`.

## Timeline (When it happened)

| Time               | Event                                                                                          |
| ------------------ | ---------------------------------------------------------------------------------------------- |
| 19 Apr 2024, 07:48 | User Gloriana received the phishing email containing the password reset link                   |
| 19 Apr 2024, 08:23 | User clicked the link and was redirected to a page where they may have supplied their password |
| 19 Apr 2024, 08:23 | The reset token was obtained by the malicious site `homespottersf[.]com`                       |

## Artifacts (Indicators of Compromise)

| Type       | Value                                              |
| ---------- | -------------------------------------------------- |
| IP address | `23.82.12[.]29`                                    |
| IP address | `185.210.95[.]57`                                  |
| URL        | `hxxp[://]homespottersf[.]com:8081/reset-password` |
| Domain     | `homespottersf[.]com`                              |

## MITRE ATT&CK Mapping

| Technique                       | ID        |
| ------------------------------- | --------- |
| Phishing: Spearphishing Link    | T1566.002 |
| User Execution: Malicious Link  | T1204.001 |
| Steal Application Access Token  | T1528     |
| Credentials from Password Store | T1555     |

## Root Cause (Why it happened)

The phishing email bypassed the email security system and was delivered to the end user. The user clicked the link and submitted their password on the attacker's page, leaking the credential. This was followed by the attacker obtaining the password reset token, allowing further account access.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Gloriana's account password was leaked.
- The user's password reset token was stolen by the attacker.
- The host device is considered compromised.

## Action Taken (How it was fixed)

- Isolated host Gloriana (172.16.17[.]172) using EDR containment.

In a real-world scenario, this would be followed by:

- Deep searching the logs and identifying unusual activity on other hosts associated with this incident.
- Investigating for lateral movement and unauthorised access.

## Recommendations (Future prevention)

1. Reset compromised user credentials.
2. Use single-use tokens for password reset and account recovery flows.
3. Run routine tests to detect system vulnerabilities.
4. Increase phishing awareness training.
