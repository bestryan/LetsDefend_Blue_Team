# SOC153: Suspicious PowerShell Script Executed

|                   |                        |
| ----------------- | ---------------------- |
| **Platform**      | LetsDefend             |
| **Severity**      | Medium                 |
| **Verdict**       | True Positive          |
| **Affected host** | Tony (172.16.17[.]206) |

---

## Summary (What happened)

On 15th Mar 2024, an alert was triggered due to a "Suspicious PowerShell Script Executed" detection. Host Tony (172.16.17[.]206) executed a malicious file (`payload_1.ps1`) fetched from AWS. Immediately after execution, an outbound connection was established to `hxxps[://]kionagranada[.]com/upload/sd2[.]ps1`, resolving to `161.22.46[.]148`.

VirusTotal identified the associated hash as a trojan, indicating a command-and-control (C2) incident. A GET request from the internal host to the C2 server succeeded, confirming live external communication. The sequence strongly indicated the host was compromised and actively executing a remote script payload.

## Timeline (When it happened — AEST)

| Time                  | Event                                                          |
| --------------------- | -------------------------------------------------------------- |
| 15 Mar 2024, 01:22:25 | File downloaded from AWS S3 bucket                             |
| 15 Mar 2024, 01:23:27 | Remote execution command run, connecting to the attacker's URL |
| 15 Mar 2024, 01:23:46 | GET request from the internal host to the C2 server succeeded  |

## Artifacts (Indicators of Compromise)

| Type            | Value                                                                   |
| --------------- | ----------------------------------------------------------------------- |
| Threat actor IP | `161.22.46[.]148`                                                       |
| Threat actor IP | `91.236.116[.]163`                                                      |
| Targeted host   | Tony (172.16.17[.]206)                                                  |
| File hash       | `db8be06ba6d2d3595dd0c86654a48cfc4c0c5408fdd3f4e1eaf342ac7a2479d0`      |
| URL             | `hxxps[://]files-ld[.]s3[.]us-east-2[.]amazonaws[.]com/payload_1[.]ps1` |
| URL             | `hxxps[://]kionagranada[.]com/upload/sd2[.]ps1`                         |

## MITRE ATT&CK Mapping

| Technique                       | ID        |
| ------------------------------- | --------- |
| PowerShell                      | T1059.001 |
| Ingress Tool Transfer           | T1105     |
| Application Layer Protocol (C2) | T1071     |

## Root Cause (Why it happened)

A malicious PowerShell script was downloaded from AWS and executed on the host, which then made an outbound connection to an identified C2 domain.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Host Tony (172.16.17[.]206) was impacted.
- Host data may have been exfiltrated.

## Action Taken (How it was fixed)

- Isolated host Tony (172.16.17[.]206) using EDR containment to prevent further data leakage.

In a real-world scenario, this would be followed by:

- Deep searching the logs and identifying unusual activity on the host.
- Checking for any sensitive information on the host that may indicate data leakage.
- Further investigating the `payload_1.ps1` malware to determine its purpose.
- Escalating to senior management for further investigation, including collaborating with legal and privacy teams to determine whether a data breach occurred.

## Recommendations (Future prevention)

1. Block malicious IPs and domains at the firewall/proxy.
2. Investigate other devices and network traffic to determine if the malware spread, and monitor for continued malicious activity.
3. Reimage the host and restore from a clean backup (after the investigation concludes).
4. Enforce endpoint IPS and auto-containment.
