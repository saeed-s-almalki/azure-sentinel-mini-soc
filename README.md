# 🛡️ Azure Sentinel Mini-SOC

Building a small but complete **Security Operations Center (SOC)** using **Microsoft Sentinel** — from log ingestion to detection engineering and incident investigation.

This project demonstrates hands-on **Blue Team / SOC Analyst** skills: deploying a SIEM, onboarding endpoints (including **on-prem / Hyper-V** machines via **Azure Arc**), writing **KQL** detections, mapping them to **MITRE ATT&CK**, simulating attacks, and investigating the resulting incidents.

> **Author:** Saeed Almalki — Senior IT Infrastructure Engineer → Cybersecurity
> 📍 Riyadh, Saudi Arabia

---

## 🎯 What this project proves

- Deploying and operating a cloud SIEM (**Microsoft Sentinel**)
- Log collection with **Azure Monitor Agent (AMA)** + **Sysmon**
- **Hybrid onboarding** of on-prem Windows machines using **Azure Arc**
- Detection engineering with **KQL** analytics rules
- Mapping detections to **MITRE ATT&CK**
- End-to-end **incident investigation** and reporting

## 🧱 Architecture

```
[ Hyper-V Windows VM / AD Domain ]  --(Azure Arc + AMA)-->  [ Log Analytics Workspace ]  -->  [ Microsoft Sentinel (SIEM) ]  -->  Alerts / Incidents
              + Sysmon                                              (Windows Security Events, Sysmon)
```

See [`docs/architecture.md`](docs/architecture.md) for the full diagram and design notes.

## 📂 Repository structure

```
azure-sentinel-mini-soc/
├── docs/            # Step-by-step build guide
│   ├── 01-lab-setup.md
│   ├── 02-connect-data-sources.md
│   ├── 03-analytics-rules.md
│   ├── 04-investigation.md
│   └── build-log.md          # ← real end-to-end deployment log (with evidence)
├── deploy/          # Infrastructure-as-Code (ARM templates)
│   ├── dcr-windows-security-sysmon.json   # AMA + Data Collection Rule
│   └── analytics-rule-bruteforce.json     # Sentinel scheduled analytics rule
├── kql/             # KQL queries (verification + detections)
├── detections/      # Detection docs + MITRE ATT&CK mapping
├── report/          # Incident report (template + completed report)
└── assets/          # Screenshots / evidence
```

## ✅ Results (this lab was actually built)

The full SOC loop was executed end-to-end against a live Azure tenant and a real on-prem Hyper-V server (`RTS-SVR1`), onboarded via Azure Arc:

**attack → collect (AMA/DCR) → detect (KQL) → analytics rule (as code) → auto-incident → investigate**

- 📓 Full deployment walkthrough with screenshots: [`docs/build-log.md`](docs/build-log.md)
- 🧾 Completed incident report: [`report/incident-report-bruteforce.md`](report/incident-report-bruteforce.md)
- 🏗️ Detection & data collection deployed as **Infrastructure-as-Code**: [`deploy/`](deploy/)

| Evidence | Screenshot |
|---|---|
| Data ingested into `SecurityEvent` | `assets/screenshots/14-securityevent-ingestion-verified.jpg` |
| Brute-force detection (20 failed logons) | `assets/screenshots/16-detection-bruteforce.jpg` |
| Auto-generated incident | `assets/screenshots/17-incident-created.jpg` |
| Incident investigation (attack story) | `assets/screenshots/18-incident-investigation-attackstory.jpg` |

## 🚀 Quick start

1. **Set up the lab** → [`docs/01-lab-setup.md`](docs/01-lab-setup.md)
   Log Analytics Workspace → enable Sentinel → onboard a Windows endpoint (Hyper-V VM via Azure Arc) → install Sysmon.
2. **Connect data sources** → [`docs/02-connect-data-sources.md`](docs/02-connect-data-sources.md)
   AMA + Data Collection Rules → Windows Security Events + Sysmon → verify with KQL.
3. **Build detections** → [`docs/03-analytics-rules.md`](docs/03-analytics-rules.md)
   Brute-force logons, suspicious PowerShell, new-process telemetry → MITRE ATT&CK.
4. **Investigate** → [`docs/04-investigation.md`](docs/04-investigation.md)
   Trigger safe attack activity → triage the incident → build a timeline → write findings.

## 🔍 Detections included

| Detection | Data source | MITRE ATT&CK |
|---|---|---|
| Brute-force / password spray (failed logons) | Security Events (4625) | T1110 |
| Suspicious PowerShell execution | Sysmon (Event 1) / 4104 | T1059.001 |
| Suspicious new process creation | Sysmon (Event 1) | T1059 / T1204 |

Full mapping: [`detections/mitre-attack-mapping.md`](detections/mitre-attack-mapping.md)

## 💰 Cost note

Sentinel bills on data ingestion. This lab is kept intentionally small and uses **your own Hyper-V VM** as the endpoint (no paid Azure VM). Using the Azure free trial + deleting resources afterward keeps cost at ~zero. See [`docs/01-lab-setup.md`](docs/01-lab-setup.md#cost-control).

## 🧰 Tools

Azure · Microsoft Sentinel · Microsoft Defender portal (unified SecOps) · Log Analytics · Azure Arc · Azure Monitor Agent · Data Collection Rules · Sysmon · KQL · MITRE ATT&CK · Infrastructure-as-Code (ARM) · Hyper-V · Active Directory

## 📄 License

MIT — see [`LICENSE`](LICENSE).
