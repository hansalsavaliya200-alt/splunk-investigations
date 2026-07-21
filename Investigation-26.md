# Investigation 26 – EDR Lateral Movement Investigation

## Scenario

The Security Operations Center (SOC) received multiple Endpoint Detection and Response (EDR) alerts indicating suspicious endpoint activity across the enterprise environment. Investigation revealed evidence of malicious DLL execution, credential dumping, remote administration abuse, command-and-control communication, file collection, archive creation, data exfiltration, and log clearing.

The objective of this investigation was to analyze endpoint telemetry, identify attacker behavior, reconstruct the attack timeline, detect Indicators of Compromise (IOCs), and recommend incident response actions using Splunk Enterprise.

---

## Objectives

- Analyze EDR telemetry.
- Detect malicious process execution.
- Identify credential dumping activity.
- Detect remote administration abuse.
- Investigate suspicious DLL execution.
- Detect command-and-control communication.
- Identify archive creation and data exfiltration.
- Detect anti-forensics activity.
- Map attacker behavior to the MITRE ATT&CK Framework.

---

## Dataset

**File:** `investigation26_edr_lateral_movement.csv`

---

# Splunk Queries Used

## Total Events

```spl
index=* source="investigation26_edr_lateral_movement.csv"
| stats count
```

---

## Event ID Distribution

```spl
index=* source="investigation26_edr_lateral_movement.csv"
| stats count by event_id
| sort event_id
```

---

## Severity Distribution

```spl
index=* source="investigation26_edr_lateral_movement.csv"
| stats count by severity
```

---

## User Statistics

```spl
index=* source="investigation26_edr_lateral_movement.csv"
| stats count by user
| sort -count
```

---

## Process Statistics

```spl
index=* source="investigation26_edr_lateral_movement.csv"
| stats count by process_name
| sort -count
```

---

## Rundll32 DLL Execution

```spl
index=* source="investigation26_edr_lateral_movement.csv"
process_name="rundll32.exe"
| table _time user hostname process_name command_line severity description
```

---

## Credential Dumping Detection

```spl
index=* source="investigation26_edr_lateral_movement.csv"
process_name="mimikatz.exe"
| table _time user hostname process_name command_line severity description
```

---

## Beacon Activity

```spl
index=* source="investigation26_edr_lateral_movement.csv"
process_name="beacon.exe"
| table _time user hostname process_name command_line severity description
```

---

## Remote Administration Abuse

```spl
index=* source="investigation26_edr_lateral_movement.csv"
(process_name="psexec.exe" OR process_name="wmic.exe")
| table _time user hostname process_name command_line severity description
```

---

## Data Collection

```spl
index=* source="investigation26_edr_lateral_movement.csv"
process_name="robocopy.exe"
| table _time user hostname process_name command_line severity description
```

---

## Archive Creation

```spl
index=* source="investigation26_edr_lateral_movement.csv"
process_name="7z.exe"
| table _time user hostname process_name command_line severity description
```

---

## Data Exfiltration

```spl
index=* source="investigation26_edr_lateral_movement.csv"
(process_name="curl.exe" OR process_name="bitsadmin.exe")
| table _time user hostname process_name command_line severity description
```

---

## Log Clearing

```spl
index=* source="investigation26_edr_lateral_movement.csv"
process_name="wevtutil.exe"
| table _time user hostname process_name command_line severity description
```

---

# Investigation Findings

## Total Events

**85 Events**

---

## Event ID Distribution

| Event ID | Description | Count |
|----------|-------------|------:|
| 4688 | Process Creation | 82 |
| 1102 | Audit Log Cleared | 1 |
| 4624 | Successful Logon | 1 |
| 5156 | Windows Filtering Platform Connection | 1 |

---

## Severity Distribution

| Severity | Count |
|----------|------:|
| Low | 70 |
| Medium | 1 |
| High | 10 |
| Critical | 4 |

---

## Most Active Users

| User | Events |
|------|------:|
| administrator | 23 |
| alex.morgan | 17 |
| it.admin | 17 |
| john.doe | 15 |
| SYSTEM | 13 |

---

## Most Executed Processes

| Process | Count |
|----------|------:|
| outlook.exe | 15 |
| svchost.exe | 15 |
| explorer.exe | 13 |
| chrome.exe | 9 |
| excel.exe | 9 |
| teams.exe | 9 |

---

# Suspicious Activities Detected

### DLL Execution

- **rundll32.exe**
- Command:

```
rundll32 payload.dll
```

Description:

```
Execute DLL
```

Severity:

```
High
```

---

### Credential Dumping

```
mimikatz.exe
```

Detected executing credential dumping functionality.

---

### Command & Control

```
beacon.exe
```

Detected communicating with attacker infrastructure.

---

### Remote Administration

```
psexec.exe
wmic.exe
```

Detected executing commands remotely across endpoints.

---

### File Collection

```
robocopy.exe
```

Used for copying sensitive files prior to exfiltration.

---

### Archive Creation

```
7z.exe
```

Used to compress collected data.

---

### Data Exfiltration

```
curl.exe
bitsadmin.exe
```

Detected transferring archived data outside the organization.

---

### Anti-Forensics

```
wevtutil.exe
```

Used to clear Windows Event Logs.

---

# Attack Timeline

| Stage | Activity |
|---------|----------|
| Initial Access | Attacker gained endpoint access. |
| DLL Execution | Malicious DLL executed using Rundll32. |
| Credential Access | Mimikatz dumped credentials. |
| Command & Control | Beacon established attacker communication. |
| Remote Administration | PsExec and WMIC executed remote commands. |
| Data Collection | Robocopy collected sensitive files. |
| Archive Creation | 7-Zip compressed collected files. |
| Data Exfiltration | Curl and Bitsadmin transferred data externally. |
| Anti-Forensics | Wevtutil cleared Windows Security logs. |

---

# Indicators of Compromise (IOCs)

### Users

- administrator

### Host

- FILESERVER01

### Suspicious Processes

- rundll32.exe
- mimikatz.exe
- beacon.exe
- powershell.exe
- psexec.exe
- wmic.exe
- robocopy.exe
- 7z.exe
- curl.exe
- bitsadmin.exe
- wevtutil.exe

---

# MITRE ATT&CK Mapping

| Technique | MITRE ID |
|------------|-----------|
| Rundll32 Execution | T1218.011 |
| OS Credential Dumping | T1003.001 |
| PowerShell | T1059.001 |
| SMB Remote Services | T1021.002 |
| Windows Management Instrumentation | T1047 |
| Data Staged | T1074.001 |
| Archive Collected Data | T1560.001 |
| Exfiltration Over Web Services | T1567 |
| Clear Windows Event Logs | T1070.001 |

---

# Incident Response Recommendations

- Immediately isolate affected endpoints.
- Disable compromised administrator accounts.
- Reset all privileged credentials.
- Block PsExec and WMIC where not required.
- Monitor Rundll32 and PowerShell execution.
- Block outbound communications from Beacon processes.
- Restrict archive utilities such as 7-Zip.
- Monitor outbound traffic for unauthorized uploads.
- Enable centralized log forwarding to prevent log tampering.
- Perform full forensic analysis on compromised hosts.

---

# Investigation Conclusion

This investigation identified a complete endpoint attack chain beginning with malicious DLL execution using Rundll32, followed by credential dumping with Mimikatz, command-and-control communication, remote administration abuse, file collection, archive creation, data exfiltration, and anti-forensics through Windows Event Log clearing.

The investigation demonstrates practical SOC analyst skills in EDR telemetry analysis, endpoint threat hunting, process investigation, IOC identification, MITRE ATT&CK mapping, attack timeline reconstruction, and incident response using Splunk Enterprise.
