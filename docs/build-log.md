# Build Log — Real Deployment

A running log of the actual hands-on build (with evidence screenshots in [`../assets/screenshots`](../assets/screenshots)).

## Stage 0 — Azure account
- Signed in to the Azure portal.
- No subscription existed; the $200 free account was not available (account previously used the offer).
- Created a **Pay-As-You-Go** subscription via *Upgrade*:
  - Subscription name: `SaeedAlmalkiLap`
  - Support plan: **Basic – Included** (free)
- Subscription became **Active** (Owner role).
- Set a **cost budget alert** to monitor billing.
- 📷 `00-account-upgraded.jpg`

## Stage 1 — Log Analytics Workspace
- Portal → **Create a resource** → *Log Analytics workspace*.
- Basics:
  - Subscription: `Subscription 1`
  - Resource group: `law-sentinel-lab` (new)
  - Name: `law-sentinel-lab`
  - Region: `UAE North`
- Review + Create → Create.
- Deployment **succeeded** ✅ (Workspace + resource group created).
- 📷 `01-create-log-analytics-workspace.jpg`, `02-workspace-review-validation.jpg`, `03-workspace-deployment-complete.jpg`

## Stage 2 — Enable Microsoft Sentinel
- Microsoft Sentinel → **Add** → selected workspace `law-sentinel-lab` → Add.
- **Sentinel free trial activated** (31 days, up to 10 GB/day free for Sentinel + Log Analytics).
- 📷 `04-sentinel-connect-workspace.jpg`, `05-sentinel-add-workspace.jpg`, `06-sentinel-free-trial-activated.jpg`
## Stage 3 — Cost control
- Budget alert set on the subscription (Cost Management → Budgets).
- Workspace → Settings → **Usage and estimated costs** → **Daily cap** set to **1 GB/day** (guardrail; well under the 10 GB/day free-trial allowance).
- 📷 `07-usage-and-daily-cap.jpg`
## Stage 4 — Onboard endpoint (Hyper-V via Azure Arc)
- Endpoint: **RTS-SVR1** — a Windows Server (domain-joined member server) running on local **Hyper-V**.
- Azure Arc → **Onboard existing machines** → *Add a single server* → **Generate script**:
  - Subscription: `Subscription 1`
  - Resource group: `law-sentinel-lab`
  - Operating system: **Windows**
  - Connectivity method: **Public endpoint**
- Ran the generated `OnboardingScript.ps1` **inside RTS-SVR1** in an elevated PowerShell → device sign-in → verification succeeded → Azure Connected Machine Agent installed.
- Machine registered in **Azure Arc → Machines**: status **🟢 Connected**, agent `1.67.03504.3207`, OS *Windows Server*, RG `law-sentinel-lab`.
- 📷 `08-azure-arc-onboard-basics.jpg`, `09-arc-onboarding-script.jpg`, `10-arc-machine-connected.jpg`
## Stage 5 — Connect data (AMA + DCR) + Sysmon
- **Portal note:** Microsoft has moved the Microsoft Sentinel experience (Overview, Content hub, Data connectors) from the Azure portal to the **Microsoft Defender portal** (unified SecOps at `security.microsoft.com`). The Azure-portal Sentinel blades now show *"This page has been moved to the Defender portal."*
  - 📷 `12-defender-portal-unified-sentinel.jpg`
- **Data collection deployed as Infrastructure-as-Code (ARM template):** [`../deploy/dcr-windows-security-sysmon.json`](../deploy/dcr-windows-security-sysmon.json), deployed via *Custom deployment → Deploy from URI* (GitHub raw). One deployment created three resources:
  - **AMA** — `AzureMonitorWindowsAgent` extension on the Arc machine (v1.44.0.0, **Succeeded**).
  - **DCR** — `dcr-win-security-sysmon` collecting Windows Security Events (→ `SecurityEvent`) and Sysmon operational logs (→ `Event`).
  - **DCRA** — association binding the DCR to `RTS-SVR1`.
  - 🐞 *Fix:* first deploy failed to submit because the AMA extension region (uaenorth) didn't match the Arc machine region (**eastus**). An extension must match its parent's region → set the extension `location` to the machine region; the DCR stays in the workspace region (uaenorth). Cross-region machine↔DCR↔workspace is supported.
- **Sysmon** installed on RTS-SVR1 (v15.21) with the SwiftOnSecurity config → logs to `Microsoft-Windows-Sysmon/Operational`.
  - 📷 `13-sysmon-installed.jpg`
- Data path: `Sysmon + Security Events → AMA → DCR → Log Analytics workspace`.
- **Ingestion verified** in Log Analytics → Logs (KQL mode): `SecurityEvent | summarize count() by EventID` returned rows (EventID 4624, 4672) — the `SecurityEvent` table is populating. 📷 `14-securityevent-ingestion-verified.jpg`
## Stage 6 — Analytics rules + MITRE ATT&CK  _(next)_
## Stage 7 — Simulate attack + investigate + report
- **Attack simulation (safe):** generated ~20 failed logons (EventID **4625**) on RTS-SVR1 via `net use \\localhost\IPC$ /user:hacker<n> <wrong-pass>` in a loop → maps to **MITRE ATT&CK T1110 (Brute Force)**. 📷 `15-simulate-failed-logons.jpg`
- **Detection verified (end-to-end):** ran the brute-force KQL — `SecurityEvent | where EventID == 4625 | summarize FailedAttempts=count(), Accounts=make_set(Account,10) by IpAddress, Computer` — returned **1 row: 20 failed attempts** from `::1` against RTS-SVR1 targeting hacker1…hacker10. The detection logic caught the simulated attack. 📷 `16-detection-bruteforce.jpg`
- _Next: promote the query to a scheduled Analytics/Detection rule → incident → investigation → report._
## Stage 7 — Simulate attack + investigate + report  _(next)_
