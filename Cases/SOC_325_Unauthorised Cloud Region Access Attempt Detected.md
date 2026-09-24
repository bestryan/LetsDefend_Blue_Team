# SOC325: Unauthorised Cloud Region Access Attempt Detected

|                      |                                   |
| -------------------- | --------------------------------- |
| **Platform**         | LetsDefend                        |
| **Severity**         | Low                               |
| **Verdict**          | True Positive                     |
| **Affected account** | test@letsdefend[.]io              |
| **Affected host**    | 52.15.206[.]21 (AWS cloud system) |

---

## Summary (What happened)

On 24th Sep 2024, an alert was triggered by repeated `POST /accounts/login HTTP/1.1 403 512` requests against the AWS cloud system. A high number of login attempts using the same user account were detected in a short period, originating from an unauthorised cloud region (one not configured for normal use). Source IP `134.209.145[.]73` and the request volume were assessed as suspicious. The host's action was already recorded as "blocked" in the alert details.

## Timeline (When it happened)

| Time               | Event                                                                                                                      |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| 24 Sep 2024, 08:21 | user test@letsdefend[.]io login attempts begin against the cloud system (approx. 50 events across firewall and proxy logs) |

## Artifacts (Indicators of Compromise)

| Type       | Value                                                  |
| ---------- | ------------------------------------------------------ |
| IP address | `134.209.145[.]73` — flagged malicious on VirusTotal   |
| CTI report | test@letsdefend[.]io account password confirmed leaked |

## MITRE ATT&CK Mapping

| Technique                        | ID        |
| -------------------------------- | --------- |
| Valid Accounts: Cloud Accounts   | T1078.004 |
| Brute Force: Credential Stuffing | T1110.004 |

## Root Cause (Why it happened)

The test@letsdefend[.]io account password had been leaked, giving the attacker valid credentials to attempt access. The login attempts originated from outside the account's normal/authorised cloud region, which triggered the alert. The environment did not enforce account lockout or MFA, which would otherwise have limited repeated attempts using the leaked password.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- The test@letsdefend[.]io account password is confirmed leaked and should be treated as compromised, though the access attempts themselves were blocked.

## Action Taken (How it was fixed)

- No further containment action was required on the host.
- Incoming requests to host 52.15.206[.]21 from outside the authorised region were already blocked by the firewall.

## Recommendations (Future prevention)

1. Reset the test account's compromised credentials.
2. Enforce account lockout and MFA.
3. Review the cloud region access rule and whitelist approved remote IPs, devices or locations within the scope of work.
4. Run routine scans to detect system vulnerabilities.
