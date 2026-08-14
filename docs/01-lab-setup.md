# 1. Lab Setup

Goal: stand up a Microsoft Sentinel workspace and onboard a Windows endpoint you already own (a **Hyper-V VM**, optionally domain-joined) — no paid Azure VM required.

## Prerequisites
- Azure account (free trial / $200 credit is enough).
- A Windows VM on **Hyper-V** (Windows 10/11 or Server) with internet access.
- Local admin on that VM.

## Steps

### 1) Create a Log Analytics Workspace (LAW)
1. Azure Portal → **Log Analytics workspaces** → **Create**.
2. Resource group: `rg-sentinel-lab` · Name: `law-sentinel-lab` · Region: closest (e.g., UAE North / West Europe).
3. Review + Create.

### 2) Enable Microsoft Sentinel
1. Portal → **Microsoft Sentinel** → **Create** → select `law-sentinel-lab`.
2. **Add**. Sentinel is now enabled on that workspace.

### 3) Onboard the endpoint
Two paths:

**A) Hyper-V / on-prem VM (recommended, free) — via Azure Arc**
1. Portal → **Azure Arc** → **Servers** → **Add** → **Generate script** (single server).
2. Run the generated script (PowerShell, as admin) **inside the Hyper-V VM**.
3. The VM appears in Azure Arc as a connected machine — now it can receive the Azure Monitor Agent.

**B) Azure VM (simplest, small cost)**
- Create a `Standard_B1s`/`B2s` Windows VM if you prefer not to use Arc.

### 4) Install Sysmon (richer logs)
Inside the VM (admin PowerShell):
```powershell
Invoke-WebRequest https://download.sysinternals.com/files/Sysmon.zip -OutFile Sysmon.zip
Expand-Archive Sysmon.zip -DestinationPath C:\Sysmon
# SwiftOnSecurity config = good baseline
Invoke-WebRequest https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml -OutFile C:\Sysmon\sysmonconfig.xml
C:\Sysmon\Sysmon64.exe -accepteula -i C:\Sysmon\sysmonconfig.xml
```

✅ Endpoint ready. Next: [connect data sources](02-connect-data-sources.md).

## Cost control
- Keep only 1 endpoint; use the free trial.
- Set a **daily cap** on the workspace (Sentinel → Settings → Usage → Daily cap).
- **Delete `rg-sentinel-lab`** when finished to stop all charges.
