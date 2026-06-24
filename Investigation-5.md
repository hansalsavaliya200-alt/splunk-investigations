# Investigation 05 - Brute Force Attack & Privilege Escalation Analysis

## Overview

This investigation focuses on detecting brute-force login activity and identifying privilege escalation events using Splunk.

The dataset contains authentication events, failed login attempts, successful logins, and privilege escalation activity from multiple users and IP addresses.

---

## Objective

The goal of this investigation was to:

* Detect brute-force attack patterns
* Analyze failed login attempts
* Identify successful logins following repeated failures
* Investigate privilege escalation events
* Determine overall security risk
* Practice SOC analyst incident investigation workflows

---

## Dataset Information

**File:** `investigation5_bruteforce.csv`

Fields:

* timestamp
* user
* src_ip
* country
* event_type
* status
* severity

---

## Investigation Questions

1. Which user generated the highest number of failed login attempts?
2. How many failed logins occurred?
3. How many successful logins occurred?
4. Which IP addresses were involved in suspicious activity?
5. Were any successful logins observed after multiple failures?
6. Were privilege escalation events detected?
7. Which users performed privilege escalation?
8. What was the highest severity observed?
9. Which account appears most suspicious?
10. What is the SOC verdict?

---

## Splunk Queries Used

### View All Events

```spl
index=* source="investigation5_bruteforce.csv"
```

### Failed Login Analysis

```spl
index=* source="investigation5_bruteforce.csv" status=Failed
| stats count by user
```

### Successful Logins

```spl
index=* source="investigation5_bruteforce.csv" status=Success
| stats count by user
```

### Severity Distribution

```spl
index=* source="investigation5_bruteforce.csv"
| stats count by severity
```

### User Activity Analysis

```spl
index=* source="investigation5_bruteforce.csv"
| stats count by user
```

### Privilege Escalation Events

```spl
index=* source="investigation5_bruteforce.csv" event_type="Privilege Escalation"
```

---

## Findings

### Failed Login Analysis

The **admin** account generated the highest number of failed login attempts.

Repeated authentication failures were observed from the same source IP before successful authentication occurred.

### Brute Force Indicators

Multiple failed logins followed by successful access strongly indicate a brute-force or password guessing attack.

### Privilege Escalation Analysis

Privilege escalation activity was observed after successful authentication.

This significantly increased the severity of the incident.

### Severity Analysis

Severity levels included:

* Low
* Medium
* High
* Critical

Critical severity events were associated with privilege escalation activity.

### Suspicious Accounts

The accounts requiring the most investigation were:

* admin
* root

Both accounts experienced repeated failed logins followed by successful access.

---

## Risk Assessment

| Indicator                       | Risk     |
| ------------------------------- | -------- |
| Multiple Failed Logins          | High     |
| Successful Login After Failures | High     |
| Privilege Escalation            | Critical |
| Administrative Account Activity | Critical |

### Overall Risk Level

**Critical**

---

## SOC Verdict

The investigation identified clear indicators of a brute-force attack against privileged accounts.

Repeated failed authentication attempts followed by successful logins and privilege escalation activity suggest a high likelihood of account compromise.

Immediate actions recommended:

* Reset affected account passwords
* Review administrative account activity
* Investigate source IP addresses
* Monitor for persistence mechanisms
* Conduct further incident response activities

---

## Skills Demonstrated

* Splunk Log Analysis
* Brute Force Detection
* Authentication Monitoring
* Privilege Escalation Investigation
* Security Event Correlation
* Incident Documentation
* SOC Analyst Methodology

---

## Key Takeaways

This investigation demonstrates how authentication events and privilege escalation activity can be correlated to identify high-risk security incidents.

Detecting repeated failed logins followed by successful access remains one of the most important SOC analyst responsibilities.

---

## Repository Structure

Investigation-05-Brute-Force-Attack-Analysis/

* README.md
* investigation5_bruteforce.csv
* screenshots/

---

**Author:** Hansal Savaliya

SOC Analyst | Splunk Learner | Cybersecurity Enthusiast
