# Splunk Investigation 06 - Windows Event Log Analysis

## Objective

Analyze Windows Security Event Logs using Splunk to identify authentication activity, privilege assignments, and suspicious process execution.

---

## Dataset

**File**

```
investigation6_windows_events.csv
```

---

## Investigation Tasks

- Count Windows Security Event IDs
- Identify successful logons
- Identify failed logons
- Detect special privilege assignments
- Analyze process creation events
- Identify suspicious administrative activity

---

## Windows Event IDs

| Event ID | Description |
|-----------|-------------|
|4624|Successful Logon|
|4625|Failed Logon|
|4672|Special Privileges Assigned|
|4688|Process Creation|

---

## Findings

### Event Distribution

| Event ID | Count |
|----------|------:|
|4624|5|
|4625|9|
|4672|2|
|4688|10|

---

### User Activity

| User | Events |
|------|-------:|
|admin|8|
|root|6|
|guest|4|
|test|4|
|John|2|
|Sarah|2|

---

### Successful Logons

- admin (3)
- guest (4)
- root (2)

---

### Failed Logons

Multiple failed authentication attempts were identified.

Highest failed login activity:

- admin
- root

---

### Privileged Accounts

Special privilege assignments detected:

- admin
- root

Event ID:

```
4672
```

---

### Process Creation

Processes observed:

- powershell.exe
- cmd.exe
- whoami.exe
- net.exe
- netstat.exe
- ipconfig.exe
- tasklist.exe
- chrome.exe
- outlook.exe

---

## Security Analysis

Observed Indicators:

- Multiple failed authentication attempts
- Administrative privilege assignments
- Command-line utilities executed
- whoami.exe executed after privilege assignment
- PowerShell activity

Possible attack sequence:

```
Authentication Attempts
        ↓
Privilege Assignment
        ↓
System Enumeration
        ↓
Administrative Commands
```

---

## Conclusion

This investigation demonstrates how Splunk can correlate Windows Security Events to identify authentication activity, privilege escalation indicators, and post-authentication reconnaissance using Event IDs 4624, 4625, 4672, and 4688.

---

## Skills Practiced

- Splunk SPL
- Windows Event Analysis
- Authentication Monitoring
- Privilege Escalation Detection
- Process Creation Analysis
- Security Investigation
