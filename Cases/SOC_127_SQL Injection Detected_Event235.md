# SOC127: SQL Injection Detected

|                     |                                          |
| ------------------- | ---------------------------------------- |
| **Platform**        | LetsDefend                               |
| **Severity**        | High                                     |
| **Verdict**         | True Positive                            |
| **Affected server** | Atlanta-Server (172.16.20[.]121, Ubuntu) |

---

## Summary (What happened)

On 7th Mar 2024, an alert was triggered due to SQL injection activity against host WebServer1000. Server Atlanta-Server (172.16.20[.]121) was under multiple SQL injection attempts, each returning HTTP status code 200, from source IP `118.194.247[.]28`. A reputation check identified the source IP as malicious, associated with China Unicom.

Decoded to human-readable form, the request was:

```
GET /?douj=3034 AND 1=1 UNION ALL SELECT 1, NULL,'<script>alert("XSS")</script>',table_name FROM information_schema.tables WHERE 2>1--/**/; EXEC xp_cmdshell('cat ../../../etc/passwd')#
```

The payload combines several techniques: a UNION-based SQL injection attempting to enumerate table names from `information_schema.tables`, an embedded `<script>` tag testing whether the application reflects output unsafely (a blind XSS check), and an `EXEC xp_cmdshell('cat ../../../etc/passwd')` attempt to read the server's password file via command execution. The user agent identifies the tool used as `sqlmap/1.7.2`, an automated SQL injection scanner, consistent with broad, automated ("spray and pray") probing rather than a manually crafted attack.

## Timeline (When it happened)

| Time                 | Event                                                                  |
| -------------------- | ---------------------------------------------------------------------- |
| 7 Mar 2024, 12:51 PM | Server Atlanta-Server (Ubuntu) allowed the SQL injection query request |

## Artifacts (Indicators of Compromise)

| Type               | Value                                                                                                                                                                                                          |
| ------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Threat actor IP    | `118.194.247[.]28`                                                                                                                                                                                             |
| Targeted server IP | `172.16.20[.]121`                                                                                                                                                                                              |
| Request            | `GET /?douj=3034 AND 1=1 UNION ALL SELECT 1, NULL,'<script>alert("XSS")</script>',table_name FROM information_schema.tables WHERE 2>1--/**/; EXEC xp_cmdshell('cat ../../../etc/passwd')#` (200, sqlmap/1.7.2) |

## MITRE ATT&CK Mapping

| Technique                         | ID    |
| --------------------------------- | ----- |
| Exploit Public-Facing Application | T1190 |
| Command and Scripting Interpreter | T1059 |
| Data from Local System            | T1005 |

## Root Cause (Why it happened)

The targeted server communicated over port 80, an insecure protocol left open for remote access. The application had unsafe handling of user-supplied input, mixing untrusted data directly into SQL command structures, which allowed the attacker to manipulate backend database queries.

## Impact (Who/What was affected)

Based on the data gathered during the investigation:

- Host Atlanta-Server (172.16.20[.]121) was impacted.
- Credentials saved on the server may have been stolen.

## Action Taken (How it was fixed)

The HTTP 200 response confirms the server processed the request, but does not by itself confirm the SQL injection or the `xp_cmdshell` attempt succeeded.

In a real-world scenario, this would be followed by:

- Deep searching the logs and identifying unusual activity on the server.
- Verifying database logs to check whether the attack succeeded.
- Escalating to senior staff for further investigation.

## Recommendations (Future prevention)

1. Review the host's open ports and close any unnecessary ones used for remote access.
2. Regularly test web applications for security issues and patch known vulnerabilities.
3. Collaborate with senior staff to investigate further impact on the environment.
