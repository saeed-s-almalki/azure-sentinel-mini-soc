# MITRE ATT&CK Mapping

How each detection in this project maps to the MITRE ATT&CK framework.

| # | Detection | Data source | Tactic | Technique | KQL |
|---|-----------|-------------|--------|-----------|-----|
| 1 | Brute-force / password spray | Security Events 4625 | Credential Access | **T1110** Brute Force | [failed-logons.kql](../kql/failed-logons.kql) |
| 2 | Suspicious PowerShell | Sysmon 1 / 4104 | Execution | **T1059.001** PowerShell | [suspicious-powershell.kql](../kql/suspicious-powershell.kql) |
| 3 | Suspicious new process (LOLBins/recon) | Sysmon 1 | Execution / Discovery | **T1059**, **T1204**, **T1087** | [new-process-sysmon.kql](../kql/new-process-sysmon.kql) |

## Coverage notes
- **Credential Access (TA0006):** failed-logon spikes catch brute force / spraying against local and domain accounts.
- **Execution (TA0002):** PowerShell + LOLBin detections cover the most common initial hands-on-keyboard activity.
- **Discovery (TA0007):** recon binaries (`whoami`, `net`, `nltest`) flag early enumeration in an AD environment.

## Next detections to add (roadmap)
- T1078 Valid Accounts — impossible-travel / off-hours logons
- T1053 Scheduled Task creation (Event 4698)
- T1003 LSASS access (Sysmon 10)
- T1021 Remote Services / lateral movement (4624 type 3/10 from unusual hosts)
