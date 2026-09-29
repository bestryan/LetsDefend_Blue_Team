# SOC250: APT35 HyperScrape Data Exfiltration Tool Detected

|                   |                                      |
| ----------------- | ------------------------------------ |
| **Platform**      | LetsDefend                           |
| **Severity**      | Medium                               |
| **Escalation**    | SOC Level 2 (escalated from Level 1) |
| **Verdict**       | True Positive                        |
| **Affected host** | Arthur (172.16.17[.]72)              |
| **Threat actor**  | APT35 / Charming Kitten              |

---

## Summary (What happened)

On 27th Dec 2023, an alert was triggered due to unusual or suspicious behaviour patterns linked to a file hash, indicating potential malicious intent. The system identified this as the APT35 HyperScrape data exfiltration tool affecting endpoint Arthur (172.16.17[.]72). The ticket was raised at Level 1 and escalated to Level 2 for further investigation into potential compromise.

Analysis of firewall, proxy and Sysmon logs found a potential remote access attempt (port 3389) from source IP `173.209.51[.]54`, whose reputation check linked it to APT35/Charming Kitten. This was followed by mailbox data being successfully downloaded from Arthur's account. The attacker tricked the user into executing a file via `explorer.exe`, which downloaded `EmailDownloader.exe` to the downloads folder. Immediately after the mailbox download, the same process initiated an HTTP connection to `136.243.108[.]14`, also linked to APT35/Charming Kitten in reputation checks — confirming the data exfiltration succeeded.

## Timeline (When it happened)

| Time               | Event                                                                            |
| ------------------ | -------------------------------------------------------------------------------- |
| 27 Dec 2023, 11:17 | Potential remote access attempt (port 3389) from source IP `173.209.51[.]54`     |
| 27 Dec 2023, 11:21 | Host Arthur (172.16.17[.]72) requested the Exchange server for a download action |
| 27 Dec 2023, 11:22 | Outbound HTTP connection to `136.243.108[.]14`                                   |

**Note:** the incident likely began with a phishing entry point or RDP access, based on evidence found in the current logs.

## Artifacts (Indicators of Compromise)

| Type            | Value                                                              |
| --------------- | ------------------------------------------------------------------ |
| Threat actor IP | `173.209.51[.]54`                                                  |
| Threat actor IP | `136.243.108[.]14`                                                 |
| Targeted host   | Arthur (172.16.17[.]72)                                            |
| File hash       | `cd2ba296828660ecd07a36e8931b851dda0802069ed926b3161745aae9aa6daa` |

## MITRE ATT&CK Mapping

| Technique                                 | ID        |
| ----------------------------------------- | --------- |
| Phishing                                  | T1566     |
| Remote Desktop Protocol                   | T1021.001 |
| User Execution: Malicious File            | T1204.002 |
| Email Collection: Remote Email Collection | T1114.002 |
| Exfiltration Over C2 Channel              | T1041     |

## Root Cause (Why it happened)

A phishing email with a malicious file, likely disguised as something benign such as `EmailDownloader.exe`, was delivered to Arthur to trick him into executing it. Once run, the malware operated in the background, connecting out and downloading mailbox data before exfiltrating it to the attacker's infrastructure.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Host Arthur (172.16.17[.]72) was impacted.
- Email information from the host was downloaded and exfiltrated.

## Action Taken (How it was fixed)

- Isolated host Arthur (172.16.17[.]72) using EDR containment to prevent further data leakage.

In a real-world scenario, this would be followed by:

- Deep searching the logs and identifying unusual activity on the host.
- Checking the mailbox for any sensitive information involved in the leak.
- Escalating to senior management for further investigation, including collaborating with legal and privacy teams to assess the breach.

## Recommendations (Future prevention)

1. Reimage the host and restore from a clean backup (after the investigation concludes).
2. Enforce endpoint IPS and auto-containment.
3. Increase phishing awareness training.
