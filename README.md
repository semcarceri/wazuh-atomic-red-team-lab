# Wazuh + Sysmon + Atomic Red Team Detection Engineering Lab

## Overview

This project demonstrates a Windows endpoint detection engineering lab using:

- **Wazuh** for SIEM monitoring and alerting
- **Sysmon** for high-fidelity endpoint telemetry
- **Atomic Red Team** for adversary simulation
- **MITRE ATT&CK** techniques for detection validation

The goal of this lab is to simulate attacker behavior, collect endpoint telemetry, and validate that the security monitoring pipeline successfully detects malicious activity.

---

# Lab Architecture

```
                  Attacker Simulation
                         
                 Atomic Red Team Tests
                         |
                         |
                         v
              Windows Endpoint Activity
                         |
                         |
                         v
                    Sysmon Logs
                         |
                         |
                         v
                  Wazuh Agent
                         |
                         |
                         v
                 Wazuh Manager
                         |
                         |
                         v
              Detection Rules & Alerts
```

---

# Components

## Wazuh Agent

The Wazuh agent is installed on the Windows endpoint and is responsible for:

- Collecting Windows event logs
- Forwarding Sysmon telemetry
- Communicating with the Wazuh Manager
- Providing endpoint visibility

---

## Sysmon

Sysmon provides enhanced visibility that native Windows logging does not provide.

Collected telemetry includes:

- Process creation
- Command-line arguments
- Parent-child process relationships
- Network connections
- Registry modifications
- File creation
- Process injection activity

---

## Atomic Red Team

Atomic Red Team is used to emulate attacker techniques mapped to MITRE ATT&CK.

The tests validate whether security controls can detect realistic adversary behavior.

---

# Detection Pipeline

```
Atomic Test
     |
     v
Windows Event
     |
     v
Sysmon Telemetry
     |
     v
Wazuh Decoder
     |
     v
Wazuh Rule
     |
     v
Security Alert
```

---

# Example Detection Flow

## Technique

```
T1059.001 - PowerShell Execution
```

## Attack Simulation

Atomic Red Team executes:

```
PowerShell command execution
```

## Telemetry Generated

Sysmon creates:

```
Event ID: 1
Process Creation
```

Containing:

```
Image:
powershell.exe

CommandLine:
<executed command>

Parent Process:
<parent process>
```

## Detection

Wazuh receives the event:

```
Sysmon Event
        |
        v
Decoder
        |
        v
Detection Rule
        |
        v
Alert
```

The alert is mapped to:

```
MITRE ATT&CK:
T1059.001 - PowerShell
```

---

# Demonstration Workflow

A successful demo should show:

### 1. Endpoint Preparation

Verify services:

```powershell
Get-Service WazuhSvc

Get-Service Sysmon64
```

---

### 2. Execute Attack Simulation

Example:

```powershell
Invoke-AtomicTest T1059.001 `
-PathToAtomicsFolder "C:\AtomicRedTeam\atomics"
```

---

### 3. Verify Sysmon Telemetry

Check generated events:

```powershell
Get-WinEvent `
-LogName "Microsoft-Windows-Sysmon/Operational" `
-MaxEvents 10
```

Expected:

```
Event ID 1 - Process Creation
```

---

### 4. Verify Wazuh Detection

In the Wazuh Dashboard:

```
Security Events
        |
        v
Agent Events
        |
        v
MITRE ATT&CK Alert
```

---

# Troubleshooting Highlights

## Sysmon Events Not Appearing in Wazuh

Cause:

The Sysmon Operational channel was not configured in the Wazuh agent.

Solution:

Add to:

```
C:\Program Files (x86)\ossec-agent\ossec.conf
```

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Restart:

```powershell
Restart-Service WazuhSvc
```

---

## Atomic Red Team Path Issues

Specify the correct atomics folder:

```powershell
-PathToAtomicsFolder "C:\AtomicRedTeam\atomics"
```

---

# Project Goals

This lab demonstrates:

- Endpoint telemetry collection
- Security monitoring architecture
- MITRE ATT&CK validation
- Detection rule testing
- SOC analyst investigation workflow

---

# Future Improvements

Possible extensions:

- Create custom Wazuh detection rules
- Build detection dashboards
- Integrate threat intelligence feeds

---
