# Investigation 9 – Credential Dumping, Lateral Movement & Data Exfiltration using Splunk

## Overview

This investigation simulates a real-world Windows domain compromise where an attacker performs credential dumping, creates a privileged account, moves laterally across multiple systems, collects sensitive files, compresses them for exfiltration, and finally attempts to hide their activity by clearing Windows Event Logs and deleting evidence.

The objective was to analyze Windows Security Event Logs using Splunk SPL, identify attacker techniques, reconstruct the attack timeline, and map findings to the MITRE ATT&CK framework.

---

# Dataset Information

**File Name**

```text
investigation9_credential_dumping.csv
```

### Windows Event IDs

| Event ID | Description                 |
| -------- | --------------------------- |
| 4624     | Successful Logon            |
| 4625     | Failed Logon                |
| 4672     | Special Privileges Assigned |
| 4688     | Process Creation            |

---

# Investigation Objectives

* Investigate credential dumping activity
* Detect Mimikatz execution
* Identify unauthorized account creation
* Detect privilege escalation
* Investigate RDP activity
* Detect lateral movement using PsExec
* Analyze data collection and staging
* Detect archive creation
* Investigate data exfiltration
* Detect anti-forensics techniques
* Build a complete attack timeline
* Produce a SOC incident report

---

# Investigation Questions & Findings

## Question 1 – Total Events

### Splunk Query

```spl
index=main source="investigation9_credential_dumping.csv"
| stats count
```

**Result:** **25 Events**

---

## Question 2 – Event ID Analysis

### Splunk Query

```spl
index=main source="investigation9_credential_dumping.csv"
| stats count by event_id
```

| Event ID | Count |
| -------- | ----: |
| 4624     |     6 |
| 4625     |     1 |
| 4672     |     1 |
| 4688     |    17 |

---

## Question 3 – User Analysis

### Splunk Query

```spl
index=main source="investigation9_credential_dumping.csv"
| stats count by user
```

| User          | Events |
| ------------- | -----: |
| backupadmin   |     13 |
| administrator |     10 |
| john          |      1 |
| sarah         |      1 |

### Finding

The attacker shifted activity from the compromised **administrator** account to the newly created **backupadmin** account.

---

## Question 4 – Severity Distribution

### Splunk Query

```spl
index=main source="investigation9_credential_dumping.csv"
| stats count by severity
```

| Severity | Count |
| -------- | ----: |
| Critical |     8 |
| High     |     4 |
| Medium   |     6 |
| Low      |     7 |

---

## Question 5 – Process Analysis

### Splunk Query

```spl
index=main source="investigation9_credential_dumping.csv"
| stats count by process_name
```

### Suspicious Processes

* powershell.exe
* mimikatz.exe
* psexec.exe
* robocopy.exe
* rar.exe
* bitsadmin.exe
* wevtutil.exe
* cmd.exe

### Findings

| Process       | Purpose            |
| ------------- | ------------------ |
| mimikatz.exe  | Credential Dumping |
| psexec.exe    | Lateral Movement   |
| robocopy.exe  | Data Collection    |
| rar.exe       | Data Compression   |
| bitsadmin.exe | Data Exfiltration  |
| wevtutil.exe  | Anti-Forensics     |

---

## Question 6 – Credential Dumping

### Commands

```text
mimikatz.exe privilege::debug
mimikatz.exe sekurlsa::logonpasswords
```

### Finding

The attacker dumped credentials from the **LSASS** process using **Mimikatz**, enabling further compromise.

---

## Question 7 – Unauthorized Account Creation

### Commands

```text
net user backupadmin P@ssw0rd! /add
net localgroup administrators backupadmin /add
```

### Finding

A new administrator account named **backupadmin** was created to maintain persistent privileged access.

---

## Question 8 – Lateral Movement

### Command

```text
psexec \\FILE01 cmd.exe
```

### Finding

The attacker used **PsExec** to move from **APP01** to **FILE01**, indicating lateral movement within the environment.

---

## Question 9 – Data Collection & Exfiltration

### Commands

```text
robocopy C:\Finance D:\Backup /E
rar a finance.rar C:\Finance
bitsadmin /transfer finance.rar
```

### Finding

Sensitive financial data was copied, compressed into **finance.rar**, and transferred using **BITSAdmin**, indicating attempted data exfiltration.

---

## Question 10 – Anti-Forensics

### Commands

```text
wevtutil cl Security
cmd.exe /c del finance.rar
```

### Finding

The attacker cleared Windows Security Event Logs and deleted the archive to remove evidence and hinder forensic investigation.

---

# Attack Timeline

| Time     | Activity                          |
| -------- | --------------------------------- |
| 09:00:12 | Administrator logon               |
| 09:01:18 | Special privileges assigned       |
| 09:02:05 | PowerShell execution              |
| 09:03:15 | Mimikatz Debug Privilege          |
| 09:03:42 | Credential Dumping                |
| 09:04:30 | backupadmin account created       |
| 09:05:15 | Administrator privileges assigned |
| 09:06:12 | Remote Desktop activity           |
| 09:07:20 | backupadmin logged into APP01     |
| 09:12:05 | PsExec lateral movement to FILE01 |
| 09:13:45 | Finance data copied               |
| 09:14:38 | finance.rar created               |
| 09:15:12 | BITSAdmin transfer                |
| 09:16:05 | Security logs cleared             |
| 09:17:10 | finance.rar deleted               |

---

# MITRE ATT&CK Mapping

| Technique                | ID        |
| ------------------------ | --------- |
| PowerShell               | T1059.001 |
| OS Credential Dumping    | T1003     |
| LSASS Memory             | T1003.001 |
| Create Account           | T1136     |
| Account Manipulation     | T1098     |
| Remote Desktop Protocol  | T1021.001 |
| PsExec / SMB             | T1021.002 |
| Data Staged              | T1074     |
| Archive Collected Data   | T1560     |
| BITS Jobs                | T1197     |
| Clear Windows Event Logs | T1070.001 |
| File Deletion            | T1070.004 |

---

# Indicators of Compromise (IOCs)

* mimikatz.exe
* privilege::debug
* sekurlsa::logonpasswords
* backupadmin
* psexec.exe
* robocopy.exe
* finance.rar
* bitsadmin.exe
* wevtutil.exe
* cmd.exe /c del finance.rar

---

# SOC Incident Summary

The investigation identified a multi-stage attack involving credential dumping, privilege escalation, account creation, lateral movement, data collection, compression, attempted data exfiltration, and anti-forensics.

The attacker used **Mimikatz** to dump credentials from LSASS, created a new privileged account (**backupadmin**), moved laterally using **PsExec**, collected sensitive financial data with **Robocopy**, compressed it using **RAR**, transferred it using **BITSAdmin**, and attempted to erase evidence by clearing Windows Event Logs and deleting the archive.

**Overall Incident Severity:** **Critical**

---

# Conclusion

This investigation demonstrates a complete enterprise attack lifecycle from credential theft through lateral movement, data theft, and anti-forensics. It highlights how multiple built-in Windows utilities and post-exploitation tools can be abused to compromise an enterprise environment while attempting to evade detection.
