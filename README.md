# Splunk SOC Investigation Lab

![Splunk](https://img.shields.io/badge/SIEM-Splunk-green)
![SOC](https://img.shields.io/badge/Role-SOC%20Analyst-blue)
![Windows](https://img.shields.io/badge/Platform-Windows%20Security-orange)
![MITRE ATT\&CK](https://img.shields.io/badge/MITRE-ATT%26CK-red)
![Status](https://img.shields.io/badge/Investigations-8%20Completed-success)

## Overview

This repository documents my hands-on **Splunk Security Operations Center (SOC)** investigations. Each investigation simulates realistic Windows security incidents involving authentication monitoring, brute-force attacks, privilege escalation, malware execution, persistence mechanisms, lateral movement, PowerShell abuse, and anti-forensics techniques.

The primary objective of this repository is to strengthen practical SIEM skills, improve Windows event log analysis, and develop real-world incident investigation capabilities using Splunk SPL.

---

# Skills Demonstrated

* Splunk SPL Query Development
* Windows Security Event Log Analysis
* Authentication Monitoring
* Brute Force Detection
* Privilege Escalation Detection
* Malware Execution Analysis
* PowerShell Threat Hunting
* Registry Persistence Detection
* Scheduled Task Analysis
* LOLBins Detection
* Lateral Movement Investigation
* Persistence Detection
* Anti-Forensics Detection
* IOC Identification
* Attack Timeline Reconstruction
* MITRE ATT&CK Mapping
* SOC Incident Reporting

---

# Investigation Portfolio

| Investigation   | Topic                                    | Status      |
| --------------- | ---------------------------------------- | ----------- |
| Investigation 1 | Windows Authentication Analysis          | ✅ Completed |
| Investigation 2 | Windows Security Event Investigation     | ✅ Completed |
| Investigation 3 | SOC Alert Investigation                  | ✅ Completed |
| Investigation 4 | Windows Event Log Analysis               | ✅ Completed |
| Investigation 5 | Brute Force Attack Detection             | ✅ Completed |
| Investigation 6 | Privilege Escalation Investigation       | ✅ Completed |
| Investigation 7 | Lateral Movement & Persistence Detection | ✅ Completed |
| Investigation 8 | Malware Execution & Registry Persistence | ✅ Completed |

---

# Investigation Workflow

Each investigation follows a structured SOC investigation process:

1. Import Windows event logs into Splunk
2. Execute SPL queries
3. Analyze Windows Event IDs
4. Identify suspicious users and processes
5. Detect attacker techniques
6. Build the attack timeline
7. Map findings to the MITRE ATT&CK framework
8. Produce a SOC incident report

---

# Windows Event IDs Covered

| Event ID | Description                 |
| -------- | --------------------------- |
| 4624     | Successful Logon            |
| 4625     | Failed Logon                |
| 4672     | Special Privileges Assigned |
| 4688     | Process Creation            |

---

# Threats Investigated

* Brute Force Attacks
* Privilege Escalation
* Malicious PowerShell
* Encoded PowerShell Commands
* Malware Execution
* Registry Run Key Persistence
* Scheduled Task Persistence
* Payload Download
* Lateral Movement
* Event Log Clearing
* Anti-Forensics
* Living-off-the-Land Binary (LOLBin) Abuse

---

# Windows Utilities (LOLBins) Investigated

* PowerShell
* Certutil
* PsExec
* WMIC
* Reg.exe
* Schtasks.exe
* BITSAdmin
* Wevtutil
* Cmd.exe
* Whoami
* Netstat
* NSLookup
* Ping
* Tasklist

---

# MITRE ATT&CK Techniques Covered

| Technique                          | ID        |
| ---------------------------------- | --------- |
| PowerShell                         | T1059.001 |
| Create Account                     | T1136     |
| Account Manipulation               | T1098     |
| Registry Run Keys / Startup Folder | T1547.001 |
| Scheduled Task / Job               | T1053.005 |
| Windows Management Instrumentation | T1047     |
| SMB / PsExec                       | T1021.002 |
| BITS Jobs                          | T1197     |
| Ingress Tool Transfer              | T1105     |
| Clear Windows Event Logs           | T1070.001 |
| System Discovery                   | T1082     |
| Network Discovery                  | T1046     |

---

# Repository Structure

```text
Splunk-SOC-Investigations/
│
├── Investigation-1/
├── Investigation-2/
├── Investigation-3/
├── Investigation-4/
├── Investigation-5/
├── Investigation-6/
├── Investigation-7/
├── Investigation-8/
└── README.md
```

Each investigation contains:

* Dataset (CSV)
* Investigation README
* Splunk SPL Queries
* Investigation Findings
* Attack Timeline
* SOC Incident Summary
* MITRE ATT&CK Mapping

---

# Key Learning Outcomes

Through these investigations I gained practical experience in:

* Developing efficient Splunk SPL queries
* Investigating Windows Security Event Logs
* Detecting malware execution and persistence
* Identifying PowerShell abuse
* Detecting Registry Run Keys and Scheduled Tasks
* Investigating LOLBin abuse
* Reconstructing complete attack timelines
* Mapping attacker behavior to the MITRE ATT&CK framework
* Writing professional SOC investigation reports

---

# Future Investigations

Upcoming investigations will focus on:

* Active Directory Attack Detection
* Kerberoasting Detection
* Pass-the-Hash Attack Analysis
* Credential Dumping Detection
* Ransomware Investigation
* DNS Tunneling Detection
* Data Exfiltration
* Insider Threat Detection
* Command and Control (C2) Traffic Analysis
* Advanced Threat Hunting with Splunk

---

# About Me

I am an aspiring **SOC Analyst** with hands-on experience in:

* Splunk SIEM
* Threat Hunting
* Windows Security Event Analysis
* Incident Investigation
* MITRE ATT&CK Mapping
* Malware Analysis Fundamentals

This repository documents my practical SOC learning journey through realistic Windows security investigations and incident response exercises.

---

## Connect With Me

* **LinkedIn:** *(Add your LinkedIn URL)*
* **GitHub:** *(Add your GitHub profile URL)*

If you found this repository helpful, feel free to ⭐ star it.
