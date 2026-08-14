# 4. Investigate the Incident

Goal: take one incident from alert → investigation → timeline → verdict, the way a SOC analyst would.

## Steps
1. Sentinel → **Incidents** → open the incident.
2. Review **alert name, severity, and the rule** that triggered it.
3. Check **entities**: user (Account), host, IP address, process.
4. Use **KQL** to pivot into surrounding logs (same host/user, ±30 min).
5. Build a short **timeline** of what happened.
6. Decide: **True Positive**, **Benign**, or **needs more investigation**.

## What to capture
- user · host · IP · timestamp · evidence · impact

## Example pivots (KQL)
```kql
// All security events for the involved account around the alert
SecurityEvent
| where Account =~ "<ACCOUNT>"
| where TimeGenerated between (datetime(<START>) .. datetime(<END>))
| order by TimeGenerated asc
```
```kql
// Processes spawned on the host during the window (Sysmon)
Event
| where Source == "Microsoft-Windows-Sysmon"
| where Computer == "<HOST>"
| order by TimeGenerated asc
```

## Write it up
Fill in [`../report/incident-report-template.md`](../report/incident-report-template.md):
Executive summary · Timeline · Findings · Recommendations. This is the part you talk through in interviews.
