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
## Stage 5 — Connect data (AMA + DCR) + verify (KQL)  _(next)_
## Stage 6 — Analytics rules + MITRE ATT&CK  _(next)_
## Stage 7 — Simulate attack + investigate + report  _(next)_
