# SOC326: Impersonating Domain MX Record Change Detected

|                         |                             |
| ----------------------- | --------------------------- |
| **Platform**            | LetsDefend                  |
| **Severity**            | Medium                      |
| **Verdict**             | True Positive               |
| **Affected host**       | Mateo (172[.]16[.]17[.]162) |
| **Impersonated domain** | letsdefwnd[.]io             |

---

## Summary (What happened)

On 17th Sep 2024, an alert was received indicating the MX record for a suspicious lookalike domain, `letsdefwnd[.]io`, had been changed. The attacker used this domain to deliver a phishing email to host Mateo (172[.]16[.]17[.]162). The email was not blocked, and the user clicked a malicious link inside it. No further file download or terminal execution was identified.

## Timeline (When it happened)

| Time               | Event                                               |
| ------------------ | --------------------------------------------------- |
| 17 Sep 2024        | MX record change detected for `letsdefwnd[.]io`     |
| 18 Sep 2024, 15:00 | Phishing email sent from `letsdefwnd[.]io` to Mateo |
| 18 Sep 2024,       | User clicked the malicious link                     |

## Artifacts (Indicators of Compromise)

| Type       | Value             |
| ---------- | ----------------- |
| IP address | `45.33.23[.]183`  |
| Domain     | `letsdefwnd[.]io` |

## MITRE ATT&CK Mapping

| Technique                      | ID        |
| ------------------------------ | --------- |
| Phishing: Spearphishing Link   | T1566.002 |
| User Execution: Malicious Link | T1204.001 |

## Root Cause (Why it happened)

The attacker registered and impersonated a lookalike domain (`letsdefwnd[.]io`) resembling the organization's legitimate domain. A phishing email disguised as a voucher offer bypassed standard spam/phishing filters and was delivered to the user, who clicked the embedded malicious link.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- If credentials were entered on the linked page, the account may be compromised.
- No direct evidence of file download or terminal execution was found on the host. Device compromise is not confirmed and should be verified through further endpoint analysis rather than assumed.

## Action Taken (How it was fixed)

- Isolated host Mateo (172[.]16[.]17[.]162) using EDR containment.
- Retracted the phishing email from the mailbox.
- Searched the email logs and identified no other affected users.

**Note:** In a real-world scenario, this would be followed by:

- Searching the logs and identifying any unusual activity on the host.
- Investigating for lateral movement.

## Recommendations (Future prevention)

1. Block the sender's domain and IP globally.
2. Reset any credentials that may have been entered on the malicious link.
3. Reimage the device and restore from a clean backup if an IOC is confirmed on the device.
