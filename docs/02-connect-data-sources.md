# 2. Connect the Data Sources

Goal: ship **Windows Security Events** and **Sysmon** logs from the endpoint into Sentinel, then confirm with KQL.

## Steps

### 1) Install the Azure Monitor Agent (AMA)
- Sentinel → **Content hub** → install **Windows Security Events** solution.
- Sentinel → **Data connectors** → **Windows Security Events via AMA** → **Open connector page**.
- **Create data collection rule (DCR)**:
  - Resources: select the Arc-connected (or Azure) VM.
  - Collect: **All Security Events** (or "Common" to reduce cost).

### 2) Collect Sysmon
- Content hub → install **Microsoft Sysmon For Windows** (or use a **Custom logs / DCR** for `Microsoft-Windows-Sysmon/Operational`).
- Add the Sysmon event log to a DCR targeting the same VM.

### 3) Verify ingestion (KQL)
In Sentinel → **Logs**, run:
```kql
// Any security events arriving?
SecurityEvent
| take 10
```
```kql
// Sysmon process-creation events (Event ID 1)
Event
| where Source == "Microsoft-Windows-Sysmon"
| take 10
```
See ready queries in [`../kql/verify-ingestion.kql`](../kql/verify-ingestion.kql).

> **Why this matters:** without data, there is nothing to detect or investigate. Confirm events flow before building rules.

✅ Data flowing. Next: [analytics rules](03-analytics-rules.md).
