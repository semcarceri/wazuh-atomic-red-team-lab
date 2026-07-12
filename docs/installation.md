1. Wazuh Agent Installation

The Wazuh agent was installed locally on the Windows endpoint using the command provided by the Wazuh Manager.
Installation Workflow

    Generated the Windows agent enrollment command on the Wazuh Manager.

    Copied the command into PowerShell on the endpoint.

    The agent downloaded, installed, and automatically registered with the manager.

    Verified installation:

powershell

Get-Service WazuhSvc

At this point, the endpoint was fully enrolled and capable of forwarding Windows logs to the Wazuh Manager.
2. Sysmon Installation

Sysmon was installed to provide high‑fidelity endpoint telemetry required for detection engineering.
Purpose

Sysmon enhances visibility into:

    Process creation

    Command‑line arguments

    Parent/child process relationships

    Network connections

    Registry modifications

    File creation

    Process access & injection

Installation Steps
powershell

.\Sysmon64.exe -accepteula -i

Verify service:
powershell

Get-Service Sysmon64

Telemetry Validation
powershell

Get-WinEvent -LogName "Microsoft-Windows-Sysmon/Operational" -MaxEvents 10

3. Atomic Red Team Exercises

Atomic Red Team was used to simulate attacker behavior mapped to MITRE ATT&CK techniques.
Installing Invoke-AtomicRedTeam
powershell

Install-Module Invoke-AtomicRedTeam,powershell-yaml -Scope CurrentUser

powershell

IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
Install-AtomicRedTeam -GetAtomics

This created:
Code

C:\AtomicRedTeam\
  ├── atomics
  ├── ExternalPayloads
  └── Invoke-AtomicRedTeam

Running Atomic Tests

Example:
powershell

Invoke-AtomicTest T1059.001 -PathToAtomicsFolder "C:\AtomicRedTeam\atomics\atomic-red-team-master\atomics"

Techniques Executed

    T1059.001 – PowerShell Execution

    T1059.003 – Windows Command Shell

    T1105 – Ingress Tool Transfer

    T1547.001 – Registry Run Keys

    T1134.002 – Create Process with Token

Detection Pipeline
Code

Atomic Test
   ↓
Windows Event
   ↓
Sysmon Telemetry
   ↓
Wazuh Decoder
   ↓
Wazuh Rule
   ↓
Alert

Example:
A PowerShell execution test generated Sysmon Event ID 1, which Wazuh ingested and mapped to MITRE ATT&CK T1059.001.
4. Troubleshooting
PowerShell Execution Policy Blocked

Problem:  
powershell-yaml.psm1 cannot be loaded because running scripts is disabled

Fix:
powershell

Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
Set-ExecutionPolicy -Scope Process Bypass
Import-Module Invoke-AtomicRedTeam

Missing Atomic Test Definitions

Problem:  
Invoke-AtomicTest T1059.001 → Cannot find path C:\AtomicRedTeam\atomics

Fix:

    Downloaded Atomic Red Team ZIP manually

    Extracted to:

Code

C:\AtomicRedTeam\atomics\atomic-red-team-master\atomics

    Provided explicit path:

powershell

-PathToAtomicsFolder "C:\AtomicRedTeam\atomics\atomic-red-team-master\atomics"

Missing Payload Scripts

Problem:  
GetToken.ps1 is not recognized during T1134.002

Fix:
powershell

Get-ChildItem C:\AtomicRedTeam -Recurse -Filter GetToken.ps1

Correct location:
Code

C:\AtomicRedTeam\atomics\T1134.002\src\GetToken.ps1

Set correct path:
powershell

$PathToAtomicsFolder="C:\AtomicRedTeam\atomics"

Sysmon Installation Misassumption

Problem:  
Attempted:
powershell

Enable-WindowsOptionalFeature -Online -FeatureName Sysmon

Sysmon is not a Windows optional feature.

Fix:  
Installed Sysmon manually from Sysinternals.
Sysmon Log Collection Not Appearing in Wazuh

Problem:  
Sysmon events were not visible in Wazuh.

Root Cause:  
The Wazuh agent was not configured to ingest the Sysmon Operational channel.

Fix:  
Edited:
Code

C:\Program Files (x86)\ossec-agent\ossec.conf

Added:
xml

<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

Restarted agent:
powershell

Restart-Service WazuhSvc

