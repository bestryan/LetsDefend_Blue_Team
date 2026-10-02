# SOC205: Malicious Macro Has Been Executed

|                   |                         |
| ----------------- | ----------------------- |
| **Platform**      | LetsDefend              |
| **Severity**      | Medium                  |
| **Verdict**       | True Positive           |
| **Affected host** | Jayne (172.16.17[.]198) |

---

## Summary (What happened)

On 28th Feb 2024, an alert was triggered by a "Suspicious file detected on system" detection. Host Jayne (172.16.17[.]198) downloaded the file `edit1-invoice.docm`, which had a macro embedded inside. Reputation checks of the macro's hash and the associated IP both returned malicious. Immediately after, the file executed via PowerShell and attempted to download `messbox.exe` ("mess.exe") from a suspicious URL — though the log showed this request returned an HTTP 404 response.

Firewall logs recorded a request to IP `92.204.211[.]16` (see note on the artifact table below regarding a discrepancy with the IOC list). IP Abuse reputation data flagged the IP under the "Hacking" and "Web App Attack" categories. The attack surface originated from Exchange, specifically Jayne's inbox, where a phishing email with a zip attachment was the initial vector.

## Timeline (When it happened — AEST)

| Time                  | Event                                                                                                                  |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| 28 Feb 2024, 16:12:23 | Phishing email from `jake.admin@cybercommunity[.]info` delivered with a zip attachment                                 |
| 28 Feb 2024, 16:41:10 | OS logs show the file downloaded via Explorer                                                                          |
| 28 Feb 2024, 16:42:00 | File executed                                                                                                          |
| 28 Feb 2024, 16:42:25 | Remote command executed, attempting to download `mess.exe` from `hxxp[://]www[.]greyhathacker[.]net/tools/messbox.exe` |
| 28 Feb 2024, 16:42:45 | Outbound request to `hxxp[://]www[.]greyhathacker[.]net/tools/messbox.exe` returned HTTP 404                           |

## Artifacts (Indicators of Compromise)

| Type            | Value                                                              |
| --------------- | ------------------------------------------------------------------ |
| Threat actor IP | `92.204.221[.]16`                                                  |
| Targeted host   | Jayne (172.16.17[.]198)                                            |
| File hash       | `1a819d18c9a9de4f81829c4cd55a17f767443c22f9b30ca953866827e5d96fb0` |
| URL             | `hxxp[://]www[.]greyhathacker[.]net/tools/messbox.exe`             |

## MITRE ATT&CK Mapping

| Technique                               | ID            |
| --------------------------------------- | ------------- |
| Phishing: Spearphishing Attachment      | T1566.001     |
| User Execution: Malicious File          | T1204.002     |
| Malicious Macro / Office Template Macro | T1137 / T1204 |
| PowerShell                              | T1059.001     |
| Ingress Tool Transfer                   | T1105         |

## Root Cause (Why it happened)

A phishing email from `jake.admin@cybercommunity[.]info` included a zip attachment that tricked the user into downloading and executing the macro-enabled file. The macro ran a PowerShell command in the background attempting to download further malware from a suspicious URL; the download attempt failed (404), limiting the attack's progression.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Host Jayne (172.16.17[.]198) was impacted.

## Action Taken (How it was fixed)

- Isolated host Jayne (172.16.17[.]198) using EDR containment to prevent further data leakage.

**Note:** In a real-world scenario, this would be followed by:

- Deep searching the logs and identifying unusual activity on the host.
- Checking for any sensitive information on the host that may indicate data leakage.
- Further investigating the malware and its purpose, using tools such as AnyRun or `exiftool` in a controlled environment to analyse metadata.

## Recommendations (Future prevention)

1. Educate employees and increase phishing awareness.
2. Block malicious IPs and domains at the firewall/proxy.
3. Ensure macro files are blocked by default settings (verify this setting has not been changed).
4. Investigate other devices and network traffic to determine if the malware spread, and monitor for continued malicious activity.
5. Reimage the host and restore from a clean backup (after the investigation concludes).
6. Enforce endpoint IPS and auto-containment.
