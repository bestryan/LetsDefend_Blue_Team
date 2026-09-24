# SOC127: SQL Injection Detected

|                   |                           |
| ----------------- | ------------------------- |
| **Platform**      | LetsDefend                |
| **Severity**      | Medium                    |
| **Verdict**       | False Positive            |
| **Affected host** | gitServer (172.16.20[.]4) |
| **Source IP**     | 172.16.20[.]5             |

---

## Summary (What happened)

On 14th Feb 2021, a SQL injection alert was triggered. Source IP `172.16.20[.]5` targeted `gitServer` (`172.16.20[.]4`) with a request containing a SQL injection payload:

```
https://172.16.20[.]4/?id=1 AND (1,2,3,4) = (SELECT * from db.users UNION SELECT 1,2,3,4 LIMIT 1)
```

The request used the GET method and the device action was "allowed." Investigation of the source showed the user agent identified as a pentest machine, indicating this was legitimate, authorized penetration testing activity rather than a real attack. The host was not contained.

## Artifacts (Indicators of Compromise)

| Type        | Value                                                                         |
| ----------- | ----------------------------------------------------------------------------- |
| Source IP   | `172.16.20[.]5`                                                               |
| Target host | gitServer (`172.16.20[.]4`)                                                   |
| Payload     | `?id=1 AND (1,2,3,4) = (SELECT * from db.users UNION SELECT 1,2,3,4 LIMIT 1)` |

## MITRE ATT&CK Mapping

| Technique                         | ID    |
| --------------------------------- | ----- |
| Exploit Public-Facing Application | T1190 |

_(Mapped for reference — activity was assessed as authorized testing, not a real attack.)_

## Root Cause (Why it happened)

A SQL injection payload was sent against `gitServer` from a host identified as a pentest machine. The traffic matches expected penetration testing activity rather than a malicious actor.

## Impact (Who/What was affected)

None. No unauthorized access or compromise occurred — the activity was consistent with an authorized security test.

## Action Taken (How it was closed)

- Reviewed the source IP and confirmed the user agent matched a known pentest machine.
- Closed the alert as a false positive; no containment action was taken.

## Recommendations (Future prevention)

1. Maintain an allowlist or tagging process for known pentest source IPs/user agents to reduce noise on future scans.
2. Confirm penetration testing schedules with the security team in advance so alerts during test windows can be triaged faster.
3. Periodically verify that `gitServer` is not actually vulnerable to the payload used, independent of the alert's source.
