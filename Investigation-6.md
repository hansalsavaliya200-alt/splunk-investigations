# 🔍 Splunk Investigation 06 – Windows Security Event Log Analysis

## 📌 Overview

This investigation focuses on analyzing Windows Security Event Logs using Splunk to identify authentication activity, failed logon attempts, privilege assignments, and process creation events. The objective is to detect suspicious behavior that may indicate brute-force attacks, privilege escalation, or post-compromise reconnaissance.

---

# 🎯 Objectives

- Analyze Windows Security Event IDs
- Identify successful and failed logon attempts
- Detect privileged account activity
- Monitor process creation events
- Investigate possible privilege escalation
- Perform basic threat hunting using Splunk SPL

---

# 🗂 Dataset

**Dataset Used**

```
investigation6_windows_events.csv
```

Source: Custom Windows Security Event Log Dataset

---

# 🛠 Splunk Searches Performed

## 1️⃣ Count All Event IDs

```spl
index=* | stats count by EventID
```

---

## 2️⃣ User Activity

```spl
index=* | stats count by user
```

---

## 3️⃣ Successful Logons (Event ID 4624)

```spl
index=* EventID=4624
```

---

## 4️⃣ Failed Logons (Event ID 4625)

```spl
index=* EventID=4625
```

---

## 5️⃣ Special Privileges Assigned (Event ID 4672)

```spl
index=* EventID=4672
```

---

## 6️⃣ Process Creation Events (Event ID 4688)

```spl
index=* EventID=4688
| stats count by process_name user
```

---

# 📊 Investigation Results

## Event ID Distribution

| Event ID | Description | Count |
|----------|-------------|------:|
|4624|Successful Logon|5|
|4625|Failed Logon|9|
|4672|Special Privileges Assigned|2|
|4688|Process Creation|10|

---

## User Activity

| User | Events |
|------|-------:|
|admin|8|
|root|6|
|guest|4|
|test|4|
|John|2|
|Sarah|2|

---

## Successful Logons

| User | Count |
|------|------:|
|admin|3|
|guest|4|
|root|2|

---

## Failed Logons

Multiple failed login attempts were detected during the investigation.

Accounts with the highest failed authentication activity:

- admin
- root

This could indicate password guessing or brute-force attempts.

---

## Privileged Accounts

Event ID **4672** indicates that special administrative privileges were assigned.

Accounts detected:

- admin
- root

These events should always be reviewed because they represent administrative access.

---

## Process Creation Events

Processes observed:

- chrome.exe
- cmd.exe
- ipconfig.exe
- net.exe
- netstat.exe
- outlook.exe
- powershell.exe
- powershell.exe
- tasklist.exe
- whoami.exe

---

# 🔎 Investigation Analysis

### Authentication Activity

- 5 successful logons
- 9 failed logons

The higher number of failed logons compared to successful logons may indicate unsuccessful login attempts against privileged accounts.

---

### Privilege Escalation Indicators

Special privilege assignments were detected for:

- admin
- root

Immediately after privileged access, administrative utilities such as:

- whoami.exe
- net.exe
- tasklist.exe
- netstat.exe
- powershell.exe

were executed.

This behavior is commonly observed during system enumeration after administrative access has been obtained.

---

### Suspicious Activity

Potential indicators include:

- Multiple failed authentication attempts
- Administrative privilege assignment
- PowerShell execution
- Command-line utilities executed
- User identity verification using whoami.exe

While these commands may be executed by legitimate administrators, they are also commonly used during post-compromise reconnaissance.

---

# 📈 Possible Attack Timeline

```
Failed Login Attempts
          │
          ▼
Successful Authentication
          │
          ▼
Special Privileges Assigned
          │
          ▼
PowerShell Execution
          │
          ▼
System Enumeration
(cmd, whoami, net, tasklist, netstat)
```

---

# 🎯 Skills Practiced

- Splunk Search Processing Language (SPL)
- Windows Security Event Analysis
- Event ID Correlation
- Authentication Monitoring
- Privilege Escalation Detection
- Process Creation Analysis
- Basic Threat Hunting
- Windows Log Investigation

---

# 📷 Screenshots

The following screenshots are included in the repository:

- Dataset Import
- Event ID Distribution
- User Activity
- Successful Logons
- Failed Logons
- Privileged Account Events
- Process Creation Analysis
- Final Investigation Dashboard

---

# ✅ Conclusion

This investigation demonstrated how Splunk can be used to analyze Windows Security Events to identify authentication activity, privilege assignments, and process execution. By correlating Event IDs **4624**, **4625**, **4672**, and **4688**, it was possible to reconstruct user activity and identify indicators commonly associated with administrative access and post-authentication reconnaissance.

---

## 🛠 Technologies Used

- Splunk Enterprise
- Windows Event Logs
- CSV Dataset
- SPL (Search Processing Language)

---

## 📚 Key Event IDs

| Event ID | Meaning |
|----------|----------------------------|
|4624|Successful Logon|
|4625|Failed Logon|
|4672|Special Privileges Assigned|
|4688|Process Creation|
