# SOC251: Quishing Detected (QR Code Phishing)

|                        |                                                   |
| ---------------------- | ------------------------------------------------- |
| **Platform**           | LetsDefend                                        |
| **Severity**           | Medium                                            |
| **Verdict**            | True Positive                                     |
| **Affected user/host** | claire@letsdefend[.]io / Claire (172.16.17[.]181) |
| **Impersonated brand** | Microsoft Security (MFA setup)                    |

---

## Summary (What happened)

On 1st Jan 2024, an alert was triggered by a "Quishing Detected (QR Code Phishing)" detection. Exchange logs showed suspicious IP `158.69.201[.]47`, whose reputation check identified it with QR phishing activity. The original email was sent from `security@microsecmfa[.]com` to `claire@letsdefend[.]io`. Logs confirmed the traffic was allowed, and the host Claire may have provided credentials as a result.

Using a QR code scanner and reader tool, the embedded QR code resolved to the URL `hxxps[://]ipfs[.]io/ipfs/Qmbr8wmr41C35c3K2GfiP2F8YGzLhYpKpb4K66KU6mLmL4#`, which pointed to `209.94.90[.]1`. Reputation checks from VirusTotal and IP Abuse both flagged this IP as highly malicious.

## Timeline (When it happened — AEST)

| Time                 | Event                                                                                                                               |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| 1 Jan 2024, 20:00:00 | Phishing email containing a QR code sent from `security@microsecmfa[.]com` to `claire@letsdefend[.]io`; traffic allowed by Exchange |

## Artifacts (Indicators of Compromise)

| Type            | Value                                                                                                    |
| --------------- | -------------------------------------------------------------------------------------------------------- |
| Threat actor IP | `158.69.201[.]47`                                                                                        |
| Threat actor IP | `209.94.90[.]1`                                                                                          |
| QR code URL     | `hxxps[://]ipfs[.]io/ipfs/Qmbr8wmr41C35c3K2GfiP2F8YGzLhYpKpb4K66KU6mLmL4#` (resolves to `209.94.90[.]1`) |
| Sender domain   | `security@microsecmfa[.]com`                                                                             |

## MITRE ATT&CK Mapping

| Technique                                                  | ID            |
| ---------------------------------------------------------- | ------------- |
| Phishing: Spearphishing via Service                        | T1566.003     |
| User Execution: Malicious Link                             | T1204.001     |
| Steal Web Session Cookie / Credentials from Password Store | T1539 / T1555 |

## Root Cause (Why it happened)

A phishing email from `security@microsecmfa[.]com` contained a QR code designed to evade traditional link/attachment scanning. The email impersonated Microsoft Security and prompted Claire to set up MFA by scanning the code, which led to a credential-harvesting page hosted via IPFS.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Host Claire (172.16.17[.]181) was impacted.

## Action Taken (How it was fixed)

- Isolated host Claire (172.16.17[.]181) using EDR containment to prevent further data leakage.
- Deleted the phishing email from Claire's mailbox.

**Note:** In a real-world scenario, this would be followed by:

- Deep searching the logs and identifying unusual activity on the host.
- Checking for any sensitive information on the host that may indicate data leakage.
- Running Exchange logs to identify whether the email was delivered to other users, and taking remediation steps accordingly.

## Recommendations (Future prevention)

1. Educate employees and increase phishing awareness, specifically around QR code (quishing) attacks.
2. Block malicious IPs and domains at the firewall/proxy.
3. Reset compromised user credentials.
4. Enforce email security controls capable of detecting QR code phishing attempts.
