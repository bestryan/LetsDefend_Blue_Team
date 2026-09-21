# SOC338: Lumma Stealer - DLL Side-Loading via ClickFix Phishing

|                   |                       |
| ----------------- | --------------------- |
| **Platform**      | LetsDefend            |
| **Event ID**      | 316                   |
| **Severity**      | Critical              |
| **Verdict**       | True Positive         |
| **Affected user** | dylan@letsdefend[.]io |

---

## Summary (What happened)

User dylan@letsdefend[.]io interacted with a malicious link in a phishing email that impersonated a Windows update. This resulted in the download and execution of a malicious file on the user's device.

## Timeline (When it happened)

| Time                  | Event                           |
| --------------------- | ------------------------------- |
| 13 Mar 2025, 09:44:00 | Phishing email delivered        |
| 13 Mar 2025, 23:26:08 | User clicked the malicious link |

## Artifacts (Indicators of Compromise)

| Type       | Value                                                          |
| ---------- | -------------------------------------------------------------- |
| URL        | `hxxps[://]overcoatpassably[.]shop/Z8UZbPyVpGfdRS/maloy[.]mp4` |
| URL        | `hxxps[://]www[.]windows-update[.]site/`                       |
| IP address | `132[.]232[.]40[.]201`                                         |
| IP address | `172[.]67[.]139[.]19`                                          |

## MITRE ATT&CK Mapping

| Technique                               | ID        |
| --------------------------------------- | --------- |
| Phishing: Spearphishing Link            | T1566.002 |
| User Execution: Malicious Link          | T1204.001 |
| User Execution: Malicious File          | T1204.002 |
| Hijack Execution Flow: DLL Side-Loading | T1574.002 |

## Root Cause (Why it happened)

The email bypassed the standard spam and phishing filters and impersonated a Windows upgrade, which led the user to click the link.

## Impact (Who/What was affected)

Dylan's device is impacted. Lumma Stealer is an information stealer, so credentials and data stored on the device, such as browser-saved passwords and sessions, should be treated as potentially exposed.

## Action Taken (How it was fixed)

- Retracted the phishing email from the mailbox.
- Contained Dylan's device for mitigation.
- Searched the email logs and identified no other affected users.

## Recommendations (Future prevention)

1. Block the sender's IP globally.
2. Increase phishing awareness training.
3. Integrate an automated email analysis solution.
4. Reset credentials and revoke active sessions for the affected user.
