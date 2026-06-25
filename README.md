# Splunk SOC Investigation Lab

![Splunk](https://img.shields.io/badge/SIEM-Splunk-green)
![SOC](https://img.shields.io/badge/Role-SOC%20Analyst-blue)
![Windows](https://img.shields.io/badge/Platform-Windows%20Security-orange)
![MITRE ATT\&CK](https://img.shields.io/badge/MITRE-ATT%26CK-red)
![Status](https://img.shields.io/badge/Investigations-7%20Completed-success)

## Overview

This repository documents my hands-on **Splunk Security Operations Center (SOC)** investigations. Each investigation simulates real-world security incidents using Windows Security Event Logs, authentication events, brute-force attacks, privilege escalation, lateral movement, and persistence techniques.

The primary goal of this repository is to strengthen my practical SIEM skills, improve log analysis capabilities, and build experience in identifying Indicators of Compromise (IOCs) using Splunk SPL.

---

# Skills Demonstrated

* Splunk SPL Queries
* Windows Event Log Analysis
* Authentication Monitoring
* Brute Force Detection
* Privilege Escalation Detection
* Lateral Movement Investigation
* Persistence Detection
* PowerShell Threat Hunting
* Process Creation Analysis
* Incident Investigation
* IOC Identification
* Attack Timeline Reconstruction
* SOC Incident Reporting
* MITRE ATT&CK Mapping

---

# Investigation List

| Investigation   | Topic                                    | Status      |
| --------------- | ---------------------------------------- | ----------- |
| Investigation 1 | Authentication Log Analysis              | ✅ Completed |
| Investigation 2 | Security Incident Investigation          | ✅ Completed |
| Investigation 3 | SOC Alert Investigation                  | ✅ Completed |
| Investigation 4 | Windows Authentication Analysis          | ✅ Completed |
| Investigation 5 | Brute Force Attack Detection             | ✅ Completed |
| Investigation 6 | Windows Event Log & Privilege Escalation | ✅ Completed |
| Investigation 7 | Lateral Movement & Persistence Detection | ✅ Completed |

---

# Investigation Workflow

Each investigation follows a structured SOC workflow:

1. Import logs into Splunk
2. Analyze Windows Event IDs
3. Execute SPL queries
4. Identify suspicious users and processes
5. Detect attacker techniques
6. Build attack timeline
7. Map techniques to MITRE ATT&CK
8. Produce SOC investigation report

---

# Windows Event IDs Covered

| Event ID | Description                 |
| -------- | --------------------------- |
| 4624     | Successful Logon            |
| 4625     | Failed Logon                |
| 4672     | Special Privileges Assigned |
| 4688     | Process Creation            |

---

# Suspicious Tools & Processes Investigated

* PowerShell
* Cmd
* PsExec
* WMIC
* Certutil
* Net.exe
* Schtasks
* BITSAdmin
* Tasklist
* Whoami
* Netstat
* NSLookup
* Ping
* Reg Query
* IPConfig

---

# MITRE ATT&CK Techniques

| Technique                          | ID        |
| ---------------------------------- | --------- |
| PowerShell                         | T1059.001 |
| Create Account                     | T1136     |
| Account Manipulation               | T1098     |
| Windows Management Instrumentation | T1047     |
| SMB / PsExec                       | T1021.002 |
| Scheduled Task                     | T1053.005 |
| BITS Jobs                          | T1197     |
| Ingress Tool Transfer              | T1105     |
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
└── README.md
```

Each investigation contains:

* Dataset (CSV)
* README.md
* Splunk SPL Queries
* Investigation Findings
* Attack Analysis
* SOC Conclusion

---

# Key Learning Outcomes

Through these investigations I gained practical experience in:

* Writing effective Splunk SPL queries
* Investigating Windows Security Events
* Detecting brute-force attacks
* Identifying privilege escalation
* Detecting malicious PowerShell activity
* Investigating lateral movement using PsExec and WMIC
* Detecting persistence using Scheduled Tasks and BITSAdmin
* Building attack timelines
* Writing SOC incident reports
* Mapping attacker behavior to the MITRE ATT&CK framework

---

# Future Investigations

Planned investigations include:

* Active Directory Attack Detection
* Ransomware Investigation
* DNS Tunneling Detection
* Data Exfiltration Investigation
* Insider Threat Analysis
* Credential Dumping Detection
* Kerberoasting Detection
* Pass-the-Hash Detection
* Web Attack Investigation
* Malware & Command-and-Control Detection

---

# About Me

I am an aspiring **SOC Analyst** with hands-on experience in:

* Splunk SIEM
* Threat Hunting
* Windows Event Analysis
* Incident Investigation
* Cybersecurity Lab Projects

This repository is part of my cybersecurity learning journey and demonstrates practical SOC investigation skills through real-world attack simulations.

---

## Connect With Me

* **LinkedIn:** *(https://www.linkedin.com/in/hansal-savaliya/)*
* **GitHub:** *(https://github.com/hansalsavaliya200-alt)*

If you find this repository helpful, feel free to ⭐ star it.
