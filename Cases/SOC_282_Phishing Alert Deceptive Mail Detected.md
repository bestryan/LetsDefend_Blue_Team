# SOC282: Phishing Alert - Deceptive Mail Detected

|                   |                       |
| ----------------- | --------------------- |
| **Platform**      | LetsDefend            |
| **Severity**      | Medium                |
| **Verdict**       | True Positive         |
| **Affected user** | felix@letsdefend[.]io |
| **Affected host** | 172.16.10[.]151       |

---

## Summary (What happened)

User felix@letsdefend[.]io interacted with a malicious link in a phishing email, resulting in a file being downloaded from an external S3 bucket. The file executed on the host, and command-and-control (C2) connections were then established.

## Timeline (When it happened)

| Time                  | Event                           |
| --------------------- | ------------------------------- |
| 13 May 2024, 09:22:00 | Phishing email delivered        |
| 13 Mar 2024, 12:59:00 | User clicked the malicious link |

## Artifacts (Indicators of Compromise)

| Type                | Value                                                                                                | Notes                                            |
| ------------------- | ---------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| IP address          | `37[.]120[.]233[.]226`                                                                               | C2, associated with AsyncRAT                     |
| URL                 | `files-ld[.]s3[.]us-east-2[.]amazonaws[.]com/59cbd215-76ea-434d-93ca-4d6aec3bac98-free-coffee[.]zip` | Payload download                                 |
| File hash (SHA-256) | `CD903AD2211CF7D166646D75E57FB866000F4A3B870B5EC759929BE2FD81D334`                                   | Confirmed malicious (Trojan) via Hybrid Analysis |

**Suspicious terminal executions (child processes):**

```
"C:\Windows\System32\cmd.exe" /c route print
"C:\Windows\System32\cmd.exe" /c ipconfig /all
"C:\Windows\System32\cmd.exe" /c tasklist /svc
"C:\Windows\System32\cmd.exe" /c net user
"C:\Windows\System32\cmd.exe" /c wmic logicaldisk get caption,description,providername
"C:\Windows\System32\cmd.exe" /c hostname
"C:\Windows\System32\cmd.exe" /c systeminfo
"C:\Windows\System32\cmd.exe"
```

These commands enumerate network configuration, running services, local user accounts, disk volumes and system information — consistent with post-execution reconnaissance.

## MITRE ATT&CK Mapping

| Technique                              | ID        |
| -------------------------------------- | --------- |
| Phishing: Spearphishing Link           | T1566.002 |
| User Execution: Malicious Link         | T1204.001 |
| System Network Configuration Discovery | T1016     |
| System Owner/User Discovery            | T1033     |
| Process Discovery                      | T1057     |
| System Information Discovery           | T1082     |
| Application Layer Protocol (C2)        | T1071     |

## Root Cause (Why it happened)

The email bypassed standard spam and phishing filters and used a too-good-to-be-true lure (a "free coffee" offer). The user clicked the link and downloaded the file, which executed in the background. The malware then established C2 to an external IP.

## Impact (Who/What was affected)

Based on the data gathered during the investigation, it is highly likely that:

- Felix's device is compromised.
- Felix's credentials are compromised.
- Sensitive information may have been exfiltrated.

## Action Taken (How it was fixed)

- Retracted the email from the mailbox.
- Isolated host Felix (172.16.10[.]151) using EDR containment.

In a real-world scenario, this would be followed by:

- Searching the email logs and identifying whether any other users were affected.
- Investigating for lateral movement and further suspicious activity.

## Recommendations (Future prevention)

1. Block the sender's IP globally.
2. Reset any compromised user credentials.
3. Educate employees and increase phishing awareness.
4. Integrate an automated email analysis solution with strong email filtering and security measures.
