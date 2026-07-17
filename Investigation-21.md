# 🛡️ Investigation 21 – Pass the Hash Attack

## Scenario

An attacker compromised a Windows environment by obtaining the NTLM hash of the local/domain Administrator account. Using Mimikatz, the attacker executed a Pass-the-Hash (PtH) attack via the `sekurlsa::pth` module to spawn a process with administrative privileges without knowing the cleartext password. The attacker then moved laterally, accessed the corporate HR file share, compressed the sensitive files using 7-Zip, exfiltrated the archived data via a BITS job, and finally cleared the Windows Security Logs using `wevtutil` to mask the compromise.

---

## Splunk Queries Used

### Total Events

```spl
index=* source="investigation21_pass_the_hash.csv"
| stats count

```

### Event ID Distribution

```spl
index=* source="investigation21_pass_the_hash.csv"
| stats count by event_id
| sort event_id

```

### Severity Distribution

```spl
index=* source="investigation21_pass_the_hash.csv"
| stats count by severity

```

### Most Active Users

```spl
index=* source="investigation21_pass_the_hash.csv"
| stats count by user
| sort -count

```

### Process Creation Events

```spl
index=* source="investigation21_pass_the_hash.csv"
event_id=4688
| table timestamp user hostname process_name command_line severity

```

### Successful Logons

```spl
index=* source="investigation21_pass_the_hash.csv"
event_id=4624
| table timestamp user hostname source_ip destination_ip

```

---

## Investigation Findings

### Total Events

* 58 Events

### Event ID Distribution

* Event ID 1102 – Audit Log Cleared
* Event ID 4624 – Successful Logon
* Event ID 4625 – Failed Logon
* Event ID 4634 – Logoff
* Event ID 4672 – Special Privileges Assigned
* Event ID 4688 – New Process Created
* Event ID 5156 – Windows Filtering Platform Connection Allowed

### Severity Distribution

* Critical – 4
* High – 2
* Medium – 16
* Low – 36

### Most Active Users

* emma.smith
* administrator
* it.admin
* priya.shah

### Suspicious Processes Observed

* mimikatz.exe
* robocopy.exe
* 7z.exe
* bitsadmin.exe
* wevtutil.exe

---

# Attack Timeline

### Step 1 – Pass-the-Hash Injection

The attacker leveraged an obtained NTLM credential hash using Mimikatz to authenticate as the Administrator without knowing the cleartext password:

```
sekurlsa::pth /user:administrator /ntlm:<hash>

```

A new command session was spawned under the security context of the `administrator`, passing the NTLM token across the network layer.

---

### Step 2 – Malicious Lateral Authentication

The injected NTLM authentication material allowed the attacker to log into multiple high-value targets across the network. A single source IP (**192.168.10.34**) successfully logged into several administrative accounts across critical hosts including `FILESERVER01` and `WIN-SEC01`.

Observed Event:

```
Successful Administrator logon to FILESERVER01 from 192.168.10.34

```

---

### Step 3 – Target Data Collection

The attacker utilized standard administrative tooling to copy sensitive data out of the central human resources repository:

* robocopy.exe (Command Line: `Copy HR share`)

---

### Step 4 – Archive Data

The collected HR files were packed together to minimize footprint and ease transport:

* 7z.exe (Command Line: `Archive HR data`)

---

### Step 5 – Data Exfiltration

The archived data was staged and exfiltrated out of the local network perimeter by creating an asynchronous background transfer job:

* bitsadmin.exe (Command Line: `Upload archive`)

---

### Step 6 – Evading Detection (Log Clearance)

To break the event forensic chain and mask the execution of Mimikatz and BITS commands, the attacker executed a security log purge:

```
wevtutil.exe Security log cleared

```

This action immediately generated a critical **Event ID 1102** tracking alert on the target host.

---

# Indicators of Compromise (IOCs)

### Suspicious Processes

* mimikatz.exe
* robocopy.exe
* 7z.exe
* bitsadmin.exe
* wevtutil.exe

### Suspicious Commands & Arguments

```
sekurlsa::pth /user:administrator /ntlm:<hash>

```

```
Copy HR share

```

```
Archive HR data

```

```
Upload archive

```

```
Security log cleared

```

### Network Indicators

* Suspicious rapid pivot logons from source IP: `192.168.10.34`

---

# MITRE ATT&CK Techniques

| Tactic | Technique |
| --- | --- |
| Defense Evasion / Lateral Movement | T1550.002 – Use of Alternate Authentication Material: Pass the Hash |
| Credential Access | T1003 – OS Credential Dumping |
| Collection | T1005 – Data from Local System |
| Archive Collected Data | T1560.001 – Archive via Utility |
| Exfiltration | T1197 – Background Intelligent Transfer Service (BITS) |
| Defense Evasion | T1070.001 – Indicator Removal: Clear Windows Event Logs |

---

## Conclusion

The analysis verifies a textbook Pass-the-Hash (PtH) attack targeting the local Administrator account context within the domain. By using Mimikatz to pass the NTLM token rather than cleartext credentials, the adversary bypassed standard password verification checks to establish unauthorized interactive network sessions from `192.168.10.34`. The threat actor systematically harvested corporate information from the HR file storage share using `robocopy`, compressed it into a single folder block via `7-Zip`, and stealthily exfiltrated the archive via `bitsadmin`. The final activity culminated in anti-forensics log purging via `wevtutil`, generating an Event ID 1102 alert. Implementing strict credential tiering, disabling local account reuse across network boundaries, and monitoring abnormal network authentication patterns from internal IP hosts are highly recommended to prevent future PtH occurrences.

```

```
