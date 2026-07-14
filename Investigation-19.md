# 🛡️ Investigation 19 – Golden Ticket Attack

## Scenario

A Domain Controller has been compromised after an attacker obtained the KRBTGT account hash. Using Mimikatz, the attacker forged a Golden Ticket, injected it into memory, authenticated as a Domain Administrator without knowing the password, accessed a file server, collected sensitive files, compressed them, exfiltrated the archive, and finally cleared Windows Security Logs to hide the evidence.

---

## Splunk Queries Used

### Total Events

```spl
index=* source="investigation19_golden_ticket.csv"
| stats count
```

### Event ID Distribution

```spl
index=* source="investigation19_golden_ticket.csv"
| stats count by event_id
| sort event_id
```

### Severity Distribution

```spl
index=* source="investigation19_golden_ticket.csv"
| stats count by severity
```

### Most Active Users

```spl
index=* source="investigation19_golden_ticket.csv"
| stats count by user
| sort -count
```

### Most Executed Processes

```spl
index=* source="investigation19_golden_ticket.csv"
| stats count by process_name
| sort -count
```

---

## Investigation Findings

### Total Events

- 56 Events

### Event ID Distribution

- Event ID 1102 – Security Log Cleared
- Event ID 4624 – Successful Logon
- Event ID 4634 – Logoff
- Event ID 4672 – Special Privileges Assigned
- Event ID 4688 – Process Creation
- Event ID 4768 – Kerberos Authentication Ticket
- Event ID 5156 – Windows Filtering Platform Connection

### Severity Distribution

- Critical – 6
- High – 2
- Medium – 22
- Low – 26

### Most Active Users

- administrator
- emma.smith
- alex.morgan

### Suspicious Processes Observed

- mimikatz.exe
- kerberos
- robocopy.exe
- 7z.exe
- bitsadmin.exe
- wevtutil.exe

---

# Attack Timeline

### Step 1 – Privilege Escalation

The attacker executed Mimikatz with:

```
privilege::debug
```

This command enables debug privileges, allowing Mimikatz to interact with protected Windows processes and manipulate Kerberos tickets.

---

### Step 2 – Golden Ticket Creation

The attacker executed:

```
kerberos::golden /ptt
```

The command creates a forged Kerberos Ticket Granting Ticket (Golden Ticket) and injects it directly into memory using Pass-The-Ticket.

---

### Step 3 – Forged Ticket Injection

Kerberos accepted the forged TGT.

Observed Event:

```
Forged TGT injected
```

The attacker now possesses a valid-looking Kerberos Ticket Granting Ticket trusted by the Domain Controller.

---

### Step 4 – Unauthorized Authentication

The attacker authenticated to FILESERVER01 using the forged Golden Ticket.

Observed Event:

```
Golden ticket logon
```

No administrator password was required because authentication relied entirely on the forged Kerberos ticket.

---

### Step 5 – Data Collection

The attacker copied sensitive files from the file server using:

- robocopy.exe

---

### Step 6 – Archive Creation

The collected files were compressed using:

- 7z.exe

---

### Step 7 – Data Exfiltration

The compressed archive was uploaded using:

- bitsadmin.exe

BITS allows attackers to transfer files in the background while appearing similar to legitimate Windows activity.

---

### Step 8 – Anti-Forensics

The attacker attempted to remove evidence by clearing Windows Security Logs using:

- wevtutil.exe

This generated:

- Event ID 1102 – Security Log Cleared

---

# Indicators of Compromise (IOCs)

### Suspicious Processes

- mimikatz.exe
- kerberos
- robocopy.exe
- 7z.exe
- bitsadmin.exe
- wevtutil.exe

### Critical Indicators

- privilege::debug
- kerberos::golden /ptt
- Forged TGT injected
- Golden ticket logon
- Security Log Cleared (1102)

---

# MITRE ATT&CK Techniques

| Tactic | Technique |
|---------|-----------|
| Credential Access | T1003 – Credential Dumping |
| Defense Evasion | T1550.003 – Golden Ticket |
| Lateral Movement | T1550 – Use of Alternate Authentication Material |
| Collection | T1005 – Data from Local System |
| Archive Collected Data | T1560.001 – Archive via Utility |
| Exfiltration | T1105 – Ingress Tool Transfer |
| Defense Evasion | T1070.001 – Clear Windows Event Logs |

---

# Conclusion

The investigation confirmed a complete Golden Ticket attack. The attacker used Mimikatz to forge a Kerberos Ticket Granting Ticket after obtaining the KRBTGT account hash. The forged ticket was injected into memory, allowing the attacker to authenticate as Domain Administrator without requiring the administrator's password. After gaining privileged access to FILESERVER01, the attacker collected confidential files, compressed them, exfiltrated the archive using BITS, and cleared Windows Security Logs to hinder forensic investigation. This attack demonstrates one of the most dangerous Active Directory persistence techniques because it allows attackers to impersonate any domain user while maintaining long-term privileged access.
