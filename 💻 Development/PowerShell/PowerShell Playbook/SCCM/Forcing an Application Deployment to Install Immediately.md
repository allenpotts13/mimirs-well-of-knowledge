---
type: powershell-pattern
status: published
created: 2026-09-18
updated: 2026-09-18
technology: PowerShell
difficulty: Advanced
tags:
  - sccm
  - configuration-manager
  - application-deployment
aliases:
  - CCM_Application Install
  - Force SCCM App Install
publish: true
permalink: powershell/forcing-an-application-deployment-to-install-immediately
---

# <span class="rune">ᚲ</span> Forcing an Application Deployment to Install Immediately

> *A deployment already targeting a machine correctly can still be sitting there, fully evaluated and simply waiting for its scheduled install time — forcing it isn't fixing a broken deployment, it's just skipping the wait.*

---

## 🎯 Problem

An application has been deployed to a machine through SCCM, but it hasn't installed yet — either because it's waiting for a scheduled maintenance window, or because the deployment evaluation needs to be manually kicked off. Force it to install now.

More generally, this pattern answers:
> How do I trigger a specific, already-targeted SCCM application deployment to install on a machine immediately, without waiting for its scheduled window?

---

## 🤔 Mental Model

Distinguish clearly between two different problems that look similar: a deployment that **hasn't been evaluated yet** (needs [[Triggering a Machine Policy Retrieval and Evaluation Cycle|a policy/evaluation cycle]] first) versus a deployment that's **already evaluated and available, just scheduled for later** (needs to be directly told to install now via the client SDK). Confusing the two leads to retriggering the wrong cycle repeatedly without actually solving anything.

---

## 🧠 Why This Pattern Works

Once an application deployment has been evaluated and is available to a client, it appears in that client's local `root\ccm\ClientSDK` WMI namespace as a `CCM_Application` instance — calling its `Install` method directly (via `Invoke-CimMethod`) triggers immediate installation, bypassing any remaining scheduled maintenance window wait time, exactly the mechanism the Software Center's own "Install" button uses internally.

---

## 💻 PowerShell Solution

```powershell
# First, ensure the deployment has actually been evaluated on this machine
Invoke-CimMethod -Namespace 'root\ccm' -ClassName 'SMS_Client' -MethodName 'TriggerSchedule' `
    -Arguments @{ sScheduleID = '{00000000-0000-0000-0000-000000000121}' }   # Application Deployment Evaluation Cycle

Start-Sleep -Seconds 15

# List available applications the client currently sees
Get-CimInstance -Namespace 'root\ccm\ClientSDK' -ClassName 'CCM_Application' |
    Select-Object Name, InstallState, EvaluationState

# Force the specific application to install now
$app = Get-CimInstance -Namespace 'root\ccm\ClientSDK' -ClassName 'CCM_Application' |
    Where-Object Name -eq 'Contoso Client 4.2'

Invoke-CimMethod -Namespace 'root\ccm\ClientSDK' -ClassName 'CCM_Application' -MethodName 'Install' -Arguments @{
    EnforcePreference = 0
    Id                = $app.Id
    IsMachineTarget   = $app.IsMachineTarget
    Priority          = 'High'
    Revision          = $app.Revision
}
```

---

## 🔄 Step-by-Step Breakdown

### Step 1 — Trigger evaluation first, in case the deployment hasn't registered yet

```powershell
Invoke-CimMethod ... -Arguments @{sScheduleID='{00000000-0000-0000-0000-000000000121}'}
```

If the application doesn't appear at all in `CCM_Application` afterward, the actual problem is upstream (targeting, collection membership, or a genuinely missing deployment) — not something this forced-install step alone can fix.

### Step 2 — Confirm the application is visible to the client

```powershell
Get-CimInstance -Namespace 'root\ccm\ClientSDK' -ClassName 'CCM_Application'
```

`InstallState`/`EvaluationState` reveal exactly where the deployment currently stands from the client's own perspective — required before attempting to force anything further.

### Step 3 — Call the Install method directly with the application's exact identifying properties

```powershell
Invoke-CimMethod ... -MethodName 'Install' -Arguments @{Id=$app.Id; Revision=$app.Revision; ...}
```

The `Install` method requires several exact matching properties (`Id`, `Revision`, `IsMachineTarget`) pulled directly from the already-retrieved `CCM_Application` instance — these can't be guessed or hardcoded generically, since they're specific to that exact application and deployment revision.

---

## 🚀 Common Use Cases

- Installing a newly deployed application on a machine immediately for testing or urgent need, without waiting for its scheduled maintenance window
- Resolving a "Software Center shows it as available but it won't install" ticket by triggering the same action programmatically
- Scripted, unattended forced installation across a batch of machines needing an urgent application rollout
- Confirming a deployment is genuinely broken (versus just scheduled for later) by attempting a forced install and observing the actual result

---

## ⚖️ Alternatives

### Remote execution against multiple machines

```powershell
$computers = 'ws001','ws002'
Invoke-Command -ComputerName $computers -ScriptBlock {
    $app = Get-CimInstance -Namespace 'root\ccm\ClientSDK' -ClassName 'CCM_Application' |
        Where-Object Name -eq 'Contoso Client 4.2'
    if ($app) {
        Invoke-CimMethod -Namespace 'root\ccm\ClientSDK' -ClassName 'CCM_Application' -MethodName 'Install' -Arguments @{
            Id = $app.Id; Revision = $app.Revision; IsMachineTarget = $app.IsMachineTarget; EnforcePreference = 0; Priority = 'High'
        }
    }
}
```

Applies the same forced install across a batch of machines in one script.

### Using Software Center's own interface (GUI equivalent, when interactive access exists)

For a machine with an interactive user present, simply opening Software Center and clicking "Install" achieves the identical result — the scripted approach is specifically valuable for unattended or remote scenarios where no user is present to click anything.

---

## ⚠️ Common Mistakes

- Attempting the forced `Install` call before confirming the application actually appears in `CCM_Application` at all — if the deployment hasn't reached the client yet, forcing evaluation first (Step 1) is the actual fix needed, not the install call itself.
- Hardcoding `Id`/`Revision` values from a different deployment or a previous version, causing the `Install` call to fail or target the wrong application entirely — these values must always be pulled fresh from the current `CCM_Application` instance.
- Assuming a successful `Install` method call guarantees a successful installation — it triggers the attempt; actual install success/failure still needs to be confirmed separately (via `InstallState` afterward or the client's own AppEnforce.log).
- Running this against a machine where the underlying deployment targeting itself is wrong (wrong collection, expired deployment), where no amount of forcing will produce a different result.

---

## 📖 Further Reading

- Microsoft Learn: "CCM_Application client WMI class reference"

---

## 💡 Wisdom from Mímir

If `CCM_Application` doesn't show the application at all, stop trying to force an install — that's not a "won't install" problem, it's a "hasn't been evaluated" or "isn't actually targeted" problem, and no amount of retrying the Install method fixes either of those.

---

## 🔗 Related Notes

- [[Triggering a Machine Policy Retrieval and Evaluation Cycle]]
- [[CIM and WMI]]
- [[Checking SCCM Client Health on a Remote Computer]]
- [[Finding a Device's SCCM Collection Membership]]
