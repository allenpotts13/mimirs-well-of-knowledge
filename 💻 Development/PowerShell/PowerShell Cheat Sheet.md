---
publish: true
status: published
permalink: powershell/powershell-cheat-sheet
---

# <span class="rune">ᛊ</span> PowerShell Cheat Sheet

> Dense reference organized by troubleshooting target. Each section links back to the full [[PowerShell Codex|concept and playbook notes]] for depth.

---

## Core Syntax Quick Reference

```powershell
$var = 'value'                        # variable assignment — see [[Variables and Data Types]]
Get-Something | Where-Object {$_.Prop -eq 'x'} | Select-Object Prop1, Prop2
Get-Something | Get-Member            # discover properties/methods — see [[Objects in PowerShell]]
foreach ($x in $collection) { }       # statement loop — see [[Loops and Iteration]]
try { ... -ErrorAction Stop } catch { $_.Exception.Message } finally { }   # see [[Error Handling]]
```

| Operator | Meaning |
|---|---|
| `-eq`, `-ne`, `-gt`, `-ge`, `-lt`, `-le` | comparison (case-insensitive by default) |
| `-like`, `-notlike` | wildcard match (`*`, `?`) |
| `-match`, `-notmatch` | regex match |
| `-contains`, `-in` | collection membership (`coll -contains x` / `x -in coll`) |
| `-and`, `-or`, `-not` / `!` | logical combination |

See: [[Objects in PowerShell]] · [[The Pipeline]] · [[Filtering and Comparison Operators]]

---

# COMPUTER

```powershell
$env:COMPUTERNAME                                          # local hostname, fastest
Get-ComputerInfo                                            # full snapshot (slow, comprehensive)
Get-CimInstance Win32_OperatingSystem | Select Caption, BuildNumber, OSArchitecture
(Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime   # uptime — see [[Checking System Uptime and Last Boot Time]]
Get-Uptime                                                   # PowerShell 7+ shortcut, local only

Get-CimInstance Win32_BIOS | Select Manufacturer, SerialNumber, SMBIOSBIOSVersion
Get-CimInstance Win32_ComputerSystem | Select Manufacturer, Model, TotalPhysicalMemory
Get-CimInstance Win32_Processor | Select Name, NumberOfCores, MaxClockSpeed
Get-CimInstance Win32_PhysicalMemory | Select DeviceLocator, Capacity, Speed

Get-PnpDevice -Status Error                                  # first triage on any hardware ticket
Get-PnpDevice -Class USB -Status OK
```

See: [[Computer and System Information Cmdlets]] · [[Hardware Inventory Cmdlets]] · [[Retrieving BIOS and System Information]] · [[Listing Connected USB and Plug-and-Play Devices]]

---

# USER

```powershell
Get-LocalUser                                                # local SAM accounts (this machine only)
Get-LocalGroupMember -Group 'Administrators'                 # local admin audit
quser /server:srv01                                          # active/disconnected sessions
logoff <sessionId>                                           # force-close a stale session

Get-ADUser -Identity jdoe -Properties LastLogonDate, MemberOf
Search-ADAccount -LockedOut                                  # find lockouts
Unlock-ADAccount -Identity jdoe                               # clear a lockout
Set-ADAccountPassword -Identity jdoe -Reset -NewPassword $securePw
Set-ADUser -Identity jdoe -ChangePasswordAtLogon $true        # NEVER skip this after a reset
Get-ADGroupMember -Identity 'Help Desk' -Recursive            # always -Recursive for a real audit
```

See: [[User and Session Cmdlets]] · [[Active Directory Cmdlets]] · [[Finding and Unlocking a Locked-Out AD Account]] · [[Resetting a User's Password via PowerShell]] · [[Managing Local Administrator Group Membership]]

---

# NETWORK

```powershell
Test-NetConnection -ComputerName srv01 -Port 443              # reachability + port, one call
Test-NetConnection -ComputerName srv01 -Port 443 -InformationLevel Quiet   # boolean, for scripts
Get-NetIPConfiguration                                        # object-based ipconfig /all
Get-NetAdapter | Select Name, Status, LinkSpeed
Restart-NetAdapter -Name 'Ethernet'                            # disable/re-enable cycle

Clear-DnsClientCache                                           # flush local resolver
Register-DnsClient                                              # re-register this machine's own record
Resolve-DnsName -Name contoso.com -Server 10.0.0.10             # bypass cache, hit authoritative server directly

tracert contoso.com                                            # hop-by-hop timing
Test-NetConnection -ComputerName contoso.com -TraceRoute        # object-based equivalent
```

See: [[Networking Cmdlets]] · [[Testing Connectivity to a Remote Host and Port]] · [[Flushing and Re-Registering DNS]] · [[Resetting a Stuck Network Adapter]] · [[Tracing a Network Path Hop by Hop]]

---

# WINDOWS (Processes, Services, Event Log)

```powershell
Get-Process | Where-Object Responding -eq $false               # find frozen processes
Stop-Process -Name notepad -Force                               # or -Id for precision
(Get-Process -Name notepad).CloseMainWindow()                   # try graceful close first

Get-Service -Name Spooler
Restart-Service -Name Spooler -Force
Get-Service -Name Spooler -DependentServices                    # check before stopping

Get-WinEvent -FilterHashtable @{LogName='System'; Level=2} -MaxEvents 20     # ALWAYS use -FilterHashtable, not Where-Object
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 20   # failed logons
Get-WinEvent -FilterHashtable @{LogName='System'; Id=6008,1074,41}           # unexpected shutdown/reboot events

Get-HotFix -Id 'KB5034441'                                       # check a specific patch
Restart-Computer -ComputerName srv01 -Force -Wait -For PowerShell -Timeout 300   # confirmed remote reboot
```

**Event `Level`:** 1=Critical · 2=Error · 3=Warning · 4=Information · 5=Verbose

See: [[Process and Service Cmdlets]] · [[Event Log Cmdlets]] · [[Finding and Killing a Frozen Process]] · [[Restarting a Hung Windows Service]] · [[Reading the Last N System Errors from the Event Log]] · [[Rebooting a Remote Computer Safely]]

---

# APPLICATIONS

```powershell
$paths = 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*',
         'HKLM:\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*'
Get-ItemProperty $paths | Where DisplayName | Select DisplayName, DisplayVersion, Publisher

# Never use Win32_Product for routine checks — slow, MSI-only, triggers reconfiguration

$app = Get-ItemProperty $paths | Where DisplayName -like '*App Name*'
Start-Process msiexec.exe -ArgumentList "/X{$guid} /qn /norestart" -Wait     # silent MSI uninstall

(Get-Item 'C:\Path\App.exe').VersionInfo | Select FileVersion, ProductVersion   # actual file version, vs registry
```

See: [[Software Inventory Cmdlets]] · [[Registry Cmdlets]] · [[Finding Installed Software on a Machine]] · [[Silently Uninstalling an Application]] · [[Checking Which Version of an Application Is Installed]] · [[Finding Which Process Is Locking a File]]

---

# STORAGE / DISK

```powershell
Get-Volume | Select DriveLetter, @{N='FreeGB';E={[math]::Round($_.SizeRemaining/1GB,2)}}, `
    @{N='PercentFree';E={[math]::Round(($_.SizeRemaining/$_.Size)*100,1)}}

Get-PhysicalDisk | Select DeviceId, MediaType, HealthStatus
Get-PhysicalDisk | Get-StorageReliabilityCounter | Select DeviceId, Temperature, ReadErrorsTotal, Wear

Get-ChildItem 'C:\' -Directory | ForEach-Object {
    [pscustomobject]@{ Folder=$_.FullName; SizeGB=[math]::Round(((Get-ChildItem $_.FullName -Recurse -File -EA SilentlyContinue | Measure Length -Sum).Sum)/1GB,2) }
} | Sort SizeGB -Descending    # measure top-level first, THEN drill into the worst one
```

See: [[Storage Cmdlets]] · [[Finding Free Disk Space Across All Drives]] · [[Checking Disk Health and SMART Status]] · [[Identifying What's Filling Up a Disk]]

---

# PERFORMANCE

```powershell
Get-Process | Sort CPU -Descending | Select -First 10 Name, CPU              # cumulative CPU time, not live %
Get-Process | Sort WorkingSet -Descending | Select -First 10 Name, `
    @{N='MemoryMB';E={[math]::Round($_.WorkingSet/1MB,1)}}

Get-Counter -Counter '\Processor(_Total)\% Processor Time' -SampleInterval 2 -MaxSamples 30
Get-Counter -ListSet 'Processor' | Select -ExpandProperty Counter            # discover counter paths
```

See: [[Finding the Top CPU or Memory-Consuming Processes]] · [[Monitoring Real-Time Resource Usage]]

---

# REMOTING

```powershell
Invoke-Command -ComputerName srv01 -ScriptBlock { Get-Service Spooler }
Invoke-Command -ComputerName srv01,srv02,srv03 -ScriptBlock { ... } | Select PSComputerName, Status

$session = New-PSSession -ComputerName srv01                                 # reuse across multiple calls
Invoke-Command -Session $session -ScriptBlock { ... }
Remove-PSSession $session

# local variable into a remote script block — pass explicitly, don't assume scope
Invoke-Command -ComputerName srv01 -ScriptBlock { param($x) ... } -ArgumentList $localVar
Invoke-Command -ComputerName srv01 -ScriptBlock { ... $using:localVar ... }
```

See: [[Remoting]] · [[CIM and WMI]]

---

# MICROSOFT 365 / ENTRA ID

```powershell
Connect-MgGraph -Scopes 'User.Read.All','Group.Read.All'     # request the MINIMUM scope needed
Connect-ExchangeOnline                                         # SEPARATE connection for mailbox data

Get-MgUser -UserId user@contoso.com -Property DisplayName, AccountEnabled
Get-MgUserLicenseDetail -UserId user@contoso.com
Get-MgUserMemberOf -UserId user@contoso.com

Get-Mailbox -Identity user@contoso.com | Select DisplayName, ForwardingSmtpAddress, ProhibitSendQuota
Get-MailboxStatistics -Identity user@contoso.com | Select TotalItemSize, ItemCount

Update-MgUser -UserId user@contoso.com -PasswordProfile @{Password='Temp!2026'; ForceChangePasswordNextSignIn=$true}

Disconnect-MgGraph
Disconnect-ExchangeOnline -Confirm:$false
```

**AzureAD / MSOnline modules are deprecated** — always use Microsoft.Graph equivalents.

See: [[Microsoft 365 and Entra ID Cmdlets]] · [[Connecting to Microsoft Graph or Exchange Online]] · [[Finding a User's Mailbox and License Details]] · [[Resetting an Entra ID User's Password]]

---

# SCCM

```powershell
# Common schedule IDs — trigger via SMS_Client.TriggerSchedule
'{00000000-0000-0000-0000-000000000021}'   # Machine Policy Retrieval & Evaluation
'{00000000-0000-0000-0000-000000000121}'   # Application Deployment Evaluation
'{00000000-0000-0000-0000-000000000113}'   # Software Update Scan
'{00000000-0000-0000-0000-000000000001}'   # Hardware Inventory

Invoke-CimMethod -Namespace 'root\ccm' -ClassName SMS_Client -MethodName TriggerSchedule `
    -Arguments @{sScheduleID='{00000000-0000-0000-0000-000000000021}'}

Get-Service CcmExec                                             # check client service first
Start-Process "$env:WinDir\CCM\CcmEval.exe" -Wait                # client health check + auto-repair

Get-CimInstance -Namespace 'root\ccm\ClientSDK' -ClassName CCM_Application | Select Name, InstallState
```

See: [[Triggering a Machine Policy Retrieval and Evaluation Cycle]] · [[Checking SCCM Client Health on a Remote Computer]] · [[Forcing an Application Deployment to Install Immediately]] · [[Finding a Device's SCCM Collection Membership]]

---

# Security & Credential Handling

```powershell
$cred = Get-Credential                                           # never hardcode plain-text passwords
$cred | Export-CliXml -Path C:\Secure\cred.xml                   # DPAPI-encrypted, tied to THIS user+machine
$cred = Import-CliXml -Path C:\Secure\cred.xml

Get-ExecutionPolicy
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser               # NOT a real security boundary — see note below
```

> Execution Policy prevents *accidental* script execution. It is explicitly **not** a security control against a deliberate attacker.

See: [[PowerShell Security Fundamentals]] · [[Testing Credentials Without Locking an Account]]

---

## 🔗 Related Notes

- [[PowerShell Codex]]
- [[SQL Cheat Sheet]]
- [[Network+ Cheat Sheet]]
