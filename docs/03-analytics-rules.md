# 3. Generate Alerts + Create Analytics Rules

Goal: create 2–3 **Scheduled analytics rules** in Sentinel, each mapped to a **MITRE ATT&CK** technique, and confirm they raise incidents.

## Create a rule (pattern)
Sentinel → **Analytics** → **Create** → **Scheduled query rule**:
1. **General:** name, severity, MITRE tactic/technique.
2. **Set rule logic:** paste the KQL (see [`../kql/`](../kql)); set entity mappings (Account, Host, IP).
3. **Query scheduling:** run every 5–15 min, lookup last 1 hour.
4. **Incident settings:** group alerts into incidents.
5. **Review + Create.**

## Rules in this project

| Rule | KQL | Severity | MITRE |
|---|---|---|---|
| Brute-force failed logons | [`failed-logons.kql`](../kql/failed-logons.kql) | Medium | T1110 |
| Suspicious PowerShell | [`suspicious-powershell.kql`](../kql/suspicious-powershell.kql) | High | T1059.001 |
| Suspicious new process | [`new-process-sysmon.kql`](../kql/new-process-sysmon.kql) | Medium | T1059 |

## Safely generate the activity
- **Failed logons:** attempt several wrong RDP/console logins on the VM → Event 4625.
- **PowerShell:** run benign-but-flagged commands (e.g., `powershell -enc <base64 of "echo test">`).
- **Advanced (optional):** use **Atomic Red Team** to run mapped techniques in the isolated lab VM only.

> **Hiring tip:** mapping each rule to MITRE ATT&CK shows you go beyond watching alerts — you build detections.

Capture screenshots of each rule + the incident it creates in [`../assets/`](../assets). Next: [investigation](04-investigation.md).
