# Wazuh Detection Demonstration

This page demonstrates the complete detection pipeline, from endpoint monitoring and attack simulation to alert generation and investigation in Wazuh.

## 1. Environment Verification

The demonstration begins by verifying that both the **Wazuh Agent** (`WazuhSvc`) and **Sysmon** services are running on the Windows virtual machine. The endpoint's IP address is then obtained using `ipconfig` and matched against the registered agent in the Wazuh dashboard, confirming that the monitored system is correctly enrolled and actively reporting.

**Video:**  
https://github.com/user-attachments/assets/c37e2fa2-04e4-46e9-a4ae-28f05dd12413

---

## 2. Attack Simulation

An **Atomic Red Team** test is executed to simulate **MITRE ATT&CK T1105 – Ingress Tool Transfer**. The exercise uses `curl.exe` to download a payload, generating realistic endpoint telemetry that is captured by Sysmon and forwarded to Wazuh for analysis.

**Video:**  
https://github.com/user-attachments/assets/139400df-e8bd-458e-ae65-af89b77c52f4

---

## 3. Detection and Investigation

The full **T1105** Atomic Red Team technique is executed. After the simulation completes, the Wazuh dashboard is revisited to verify that the total number of alerts has increased. The generated alerts are then filtered using **DQL** on the `rule.mitre.id` field to display only events associated with **MITRE ATT&CK T1105**, demonstrating how Wazuh enables analysts to quickly identify and investigate activity related to a specific ATT&CK technique.

**Video:**  
https://github.com/user-attachments/assets/e3b0f836-0f45-4223-927f-34168f67845b
