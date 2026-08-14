# Architecture

```
┌─────────────────────────────┐        Azure Arc + AMA        ┌──────────────────────────┐
│  Hyper-V Windows VM          │   (Data Collection Rules)     │  Log Analytics Workspace │
│  • Windows Security Events   │ ────────────────────────────▶ │  law-sentinel-lab        │
│  • Sysmon (process/net/reg)  │                               │                          │
│  • (optional) AD Domain      │                               └────────────┬─────────────┘
└─────────────────────────────┘                                             │
                                                                            ▼
                                                            ┌──────────────────────────────┐
                                                            │  Microsoft Sentinel (SIEM)   │
                                                            │  • Analytics rules (KQL)     │
                                                            │  • Incidents / investigation │
                                                            │  • MITRE ATT&CK mapping      │
                                                            └──────────────────────────────┘
```

## Design notes
- **Endpoint = your own Hyper-V VM** → no paid Azure compute. Onboarded to Azure via **Azure Arc**, then the **Azure Monitor Agent** streams logs through **Data Collection Rules** into the workspace.
- **Sysmon** enriches native Windows logging (process creation, network, registry) for higher-fidelity detections.
- **Sentinel** sits on top of the Log Analytics Workspace: scheduled KQL rules generate alerts → grouped into incidents → investigated → documented.
- Optional **Active Directory domain** on Hyper-V makes logon/lateral-movement detections realistic.

## Data flow
1. VM generates Security + Sysmon events.
2. AMA (via DCR) forwards selected events to the workspace.
3. Sentinel analytics rules run KQL on a schedule.
4. Matches raise alerts → incidents.
5. Analyst investigates, builds a timeline, writes the report.
