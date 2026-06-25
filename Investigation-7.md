# Investigation 7 – Lateral Movement & Persistence Detection using Splunk

## Overview

This investigation analyzes a simulated Windows security incident involving privilege escalation, lateral movement, malicious PowerShell execution, account creation, and persistence mechanisms. The objective was to investigate attacker activity using Splunk SPL queries, reconstruct the attack timeline, and identify Indicators of Compromise (IOCs).

---

# Dataset Information

**File Name**

```
investigation7_powershell_attack.csv
```

**Windows Event IDs**

| Event ID | Description                 |
| -------- | --------------------------- |
| 4624     | Successful Logon            |
| 4625     | Failed Logon                |
| 4672     | Special Privileges Assigned |
| 4688     | Process Creation            |

---

# Investigation Objectives

* Analyze Windows Security Events
* Detect malicious PowerShell activity
* Identify privilege escalation
* Detect unauthorized account creation
* Investigate lateral movement
* Detect persistence mechanisms
* Build an attack timeline
* Produce a SOC investigation summary

---

# Investigation Questions & Findings

## Question 1 – Total Events

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
| stats count
```

### Result

**Total Events:** 25

---

## Question 2 – Event ID Analysis

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
| stats count by event_id
```

### Result

| Event ID | Description                 | Count |
| -------- | --------------------------- | ----: |
| 4624     | Successful Logon            |     2 |
| 4625     | Failed Logon                |     2 |
| 4672     | Special Privileges Assigned |     2 |
| 4688     | Process Creation            |    19 |

### Analysis

Most events were Process Creation (4688), indicating extensive command execution after system compromise.

---

## Question 3 – User Activity

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
| stats count by user
```

### Result

| User   | Events |
| ------ | -----: |
| admin  |      9 |
| hacker |     16 |

### Analysis

The attacker created and later used the **hacker** account extensively after compromising the administrator account.

---

## Question 4 – Severity Analysis

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
| stats count by severity
```

### Result

| Severity | Count |
| -------- | ----: |
| Critical |     8 |
| High     |     4 |
| Medium   |     6 |
| Low      |     7 |

### Analysis

Nearly half of the events were High or Critical severity, indicating a significant security incident requiring immediate investigation.

---

## Question 5 – Suspicious Process Analysis

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
| stats count by process_name
```

### Suspicious Processes

* powershell.exe
* cmd.exe
* net.exe
* psexec.exe
* wmic.exe
* certutil.exe
* schtasks.exe
* bitsadmin.exe
* whoami.exe
* tasklist.exe
* ipconfig.exe
* netstat.exe
* nslookup.exe
* ping.exe
* reg.exe

### Analysis

The combination of encoded PowerShell, PsExec, WMIC, Certutil, Scheduled Tasks, and BITSAdmin strongly indicates malicious post-exploitation activity.

---

## Question 6 – PowerShell Analysis

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
process_name="powershell.exe"
| table timestamp user command_line
```

### Findings

* ExecutionPolicy Bypass
* Encoded PowerShell (-enc)
* Invoke-WebRequest evil.ps1

### Analysis

The attacker bypassed PowerShell execution restrictions, executed encoded commands, and downloaded a malicious PowerShell script.

---

## Question 7 – Encoded PowerShell Detection

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
command_line="*enc*"
```

### Finding

```
powershell.exe -enc SQBFAFgA
```

Executed by:

```
admin
```

### Analysis

Encoded PowerShell commands are commonly used to evade detection and execute hidden malicious payloads.

---

## Question 8 – Unauthorized User Creation

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
command_line="*net user*"
```

### Finding

```
net user hacker Pass@123 /add
```

Executed by:

```
admin
```

### Analysis

A new local administrator account named **hacker** was created, indicating attacker persistence.

---

## Question 9 – Administrator Group Modification

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
command_line="*administrators*"
```

### Finding

```
net localgroup administrators hacker /add
```

### Analysis

The attacker elevated the newly created account by adding it to the local Administrators group.

---

## Question 10 – Lateral Movement using PsExec

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
process_name="psexec.exe"
```

### Finding

```
psexec \\WIN-PC02 cmd.exe
```

Source Host:

```
WIN-SRV01
```

Target Host:

```
WIN-PC02
```

Executed by:

```
admin
```

### Analysis

PsExec was used to remotely execute commands on WIN-PC02, confirming lateral movement within the environment.

---

## Question 11 – Remote Execution using WMIC

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
process_name="wmic.exe"
```

### Finding

```
wmic process call create powershell.exe
```

Executed by:

```
admin
```

Host:

```
WIN-SRV01
```

### Analysis

WMIC was used to remotely spawn a PowerShell process, providing additional evidence of lateral movement.

---

## Question 12 – Persistence Detection

### Splunk Query

```spl
index=main source="investigation7_powershell_attack.csv"
(process_name="schtasks.exe" OR process_name="bitsadmin.exe")
| table timestamp hostname user process_name command_line severity
```

### Findings

#### Scheduled Task

```
schtasks /create /sc minute /tn updater
```

#### BITSAdmin

```
bitsadmin /transfer payload.exe
```

Executed by:

```
hacker
```

Host:

```
WIN-PC02
```

### Analysis

The attacker established persistence by creating a scheduled task and used BITSAdmin to download an additional payload while blending into normal Windows background activity.

---

# Attack Timeline

| Time        | Activity                            |
| ----------- | ----------------------------------- |
| 10:00:15    | Administrator successful logon      |
| 10:02:10    | Special privileges assigned         |
| 10:03:01    | Execution Policy Bypass             |
| 10:03:22    | Encoded PowerShell executed         |
| 10:04:05    | whoami executed                     |
| 10:04:33    | Created hacker account              |
| 10:05:12    | Added hacker to Administrators      |
| 10:06:15    | PsExec executed to WIN-PC02         |
| 10:07:40    | WMIC executed                       |
| 10:08:05    | Successful hacker login on WIN-PC02 |
| 10:08:30    | Special privileges assigned         |
| 10:09:15    | Downloaded evil.ps1                 |
| 10:10:10    | Downloaded payload using Certutil   |
| 10:11–10:18 | System discovery commands executed  |
| 10:19:55    | Scheduled Task created              |
| 10:20:35    | BITSAdmin payload transfer          |

---

# MITRE ATT&CK Techniques

| Technique                          | ID        |
| ---------------------------------- | --------- |
| PowerShell                         | T1059.001 |
| Create Account                     | T1136     |
| Account Manipulation               | T1098     |
| Windows Management Instrumentation | T1047     |
| PsExec                             | T1021.002 |
| Scheduled Task                     | T1053.005 |
| BITS Jobs                          | T1197     |
| Ingress Tool Transfer              | T1105     |
| System Discovery                   | T1082     |
| Network Discovery                  | T1046     |

---

# Indicators of Compromise (IOCs)

* Encoded PowerShell execution
* Execution Policy Bypass
* Unauthorized account creation
* Administrator group modification
* PsExec execution
* WMIC remote execution
* Certutil payload download
* Scheduled Task creation
* BITSAdmin payload transfer

---

# Conclusion

This investigation identified a complete attack chain beginning with administrative access and progressing through privilege escalation, malicious PowerShell execution, account creation, lateral movement, system discovery, payload delivery, and persistence.

The attacker successfully moved from **WIN-SRV01** to **WIN-PC02**, established a privileged backdoor account, downloaded additional payloads, and configured persistence mechanisms to maintain long-term access.

**Overall Incident Severity:** **Critical**
