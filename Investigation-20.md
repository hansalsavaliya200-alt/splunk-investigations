# 🛡️ Investigation 20 – Silver Ticket Attack

## Scenario

An attacker compromised a Windows environment by obtaining the NTLM hash of a service account used by the CIFS (SMB) service. Using Mimikatz, the attacker forged a Silver Ticket for FILESERVER01, injected it into memory using Pass-The-Ticket, accessed confidential file shares without contacting the Domain Controller, archived sensitive data, exfiltrated it using BITS, removed local evidence, and finally cleared Windows Security Logs to hide malicious activity.

---

## Splunk Queries Used

### Total Events

```spl
index=* source="investigation20_silver_ticket.csv"
| stats count
```

### Event ID Distribution

```spl
index=* source="investigation20_silver_ticket.csv"
| stats count by event_id
| sort event_id
```

### Severity Distribution

```spl
index=* source="investigation20_silver_ticket.csv"
| stats count by severity
```

### Most Active Users

```spl
index=* source="investigation20_silver_ticket.csv"
| stats count by user
| sort -count
```

### Most Executed Processes

```spl
index=* source="investigation20_silver_ticket.csv"
| stats count by process_name
| sort -count
```

### Process Creation Events

```spl
index=* source="investigation20_silver_ticket.csv"
event_id=4688
| table timestamp user hostname process_name command_line severity
```

### Successful Logons

```spl
index=* source="investigation20_silver_ticket.csv"
event_id=4624
| table timestamp user hostname logon_type service severity
```

---

## Investigation Findings

### Total Events

- 59 Events

### Event ID Distribution

- Event ID 1102 – Security Log Cleared
- Event ID 4624 – Successful Logon
- Event ID 4634 – Logoff
- Event ID 4672 – Special Privileges Assigned
- Event ID 4688 – Process Creation
- Event ID 5156 – Windows Filtering Platform Connection

### Severity Distribution

- Critical – 5
- High – 3
- Medium – 17
- Low – 34

### Most Active Users

- administrator
- anil.patel

### Suspicious Processes Observed

- mimikatz.exe
- robocopy.exe
- 7z.exe
- bitsadmin.exe
- wevtutil.exe

---

# Attack Timeline

### Step 1 – Silver Ticket Creation

The attacker executed Mimikatz using:

```
kerberos::golden /service:cifs /target:FILESERVER01 /ptt
```

Although the command uses `kerberos::golden`, the `/service:cifs` and `/target:FILESERVER01` parameters indicate a **Silver Ticket attack**. A forged Kerberos Service Ticket (TGS) was created for the CIFS service on FILESERVER01 and injected into memory using Pass-The-Ticket.

---

### Step 2 – Service Authentication

The forged Silver Ticket was accepted by FILESERVER01, allowing the attacker to authenticate directly to the CIFS (SMB) service without requesting a ticket from the Domain Controller.

Observed Event:

```
Successful Administrator logon to FILESERVER01
```

---

### Step 3 – Data Collection

The attacker copied confidential shared files using:

- robocopy.exe

---

### Step 4 – Archive Files

The stolen files were compressed using:

- 7z.exe

---

### Step 5 – Data Exfiltration

The archive was uploaded using:

- bitsadmin.exe

BITS allows attackers to transfer files in the background while blending in with legitimate Windows activity.

---

### Step 6 – Cleanup

The attacker removed local evidence using:

```
cmd.exe
Delete archive
```

---

### Step 7 – Clear Security Logs

The attacker cleared Windows Security Logs using:

- wevtutil.exe

This generated:

- Event ID 1102 – Security Log Cleared

---

# Indicators of Compromise (IOCs)

### Suspicious Processes

- mimikatz.exe
- robocopy.exe
- 7z.exe
- bitsadmin.exe
- wevtutil.exe

### Suspicious Commands

```
kerberos::golden /service:cifs /target:FILESERVER01 /ptt
```

```
Copy confidential share
```

```
Archive confidential data
```

```
Upload archive
```

```
Delete archive
```

```
Clear Security log
```

---

# MITRE ATT&CK Techniques

| Tactic | Technique |
|---------|-----------|
| Credential Access | T1558.002 – Silver Ticket |
| Defense Evasion | T1550 – Use of Alternate Authentication Material |
| Collection | T1005 – Data from Local System |
| Archive Collected Data | T1560.001 – Archive via Utility |
| Exfiltration | T1105 – Ingress Tool Transfer |
| Defense Evasion | T1070.001 – Clear Windows Event Logs |

---

# Conclusion

The investigation confirmed a Silver Ticket attack against the CIFS service on FILESERVER01. The attacker forged a Kerberos Service Ticket using the compromised service account hash and injected it into memory with Mimikatz. Unlike a Golden Ticket attack, this allowed authentication only to the targeted service without requiring communication with the Domain Controller. After gaining unauthorized access to confidential file shares, the attacker copied sensitive files, compressed them into an archive, exfiltrated the data using BITS, deleted local evidence, and cleared Windows Security Logs to hinder forensic investigation. This attack demonstrates how compromised service account credentials can be abused to access specific network services while remaining stealthier than traditional Golden Ticket attacks.
