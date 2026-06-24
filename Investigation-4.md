# Investigation 04 - Security Incident Analysis

## Overview

This investigation focuses on identifying suspicious authentication activity, brute-force indicators, and privilege escalation events using Splunk.

The dataset contains login attempts, service logins, file access events, and privilege escalation activities originating from multiple countries and IP addresses.

---

## Objective

The goal of this investigation was to:

* Analyze authentication events
* Detect repeated failed logins
* Identify successful logins following failures
* Investigate suspicious IP addresses
* Detect privilege escalation activity
* Assess overall security risk

---

## Dataset Information

**File:** `security_incidents.csv`

Fields:

* timestamp
* user
* src_ip
* country
* event_type
* severity
* status

---

## Investigation Questions

1. Which user generated the highest number of events?
2. Which IP address generated the highest number of events?
3. Which country generated the most security events?
4. How many failed login attempts occurred?
5. How many successful logins occurred?
6. Which users experienced repeated authentication failures?
7. Were any successful logins observed after multiple failures?
8. Were any privilege escalation events detected?
9. Which accounts performed privilege escalation?
10. What is the overall security risk level?

---

## Splunk Queries Used

### View All Events

```spl
index=* source="security_incidents.csv"
```

### Event Count by User

```spl
index=* source="security_incidents.csv"
| stats count by user
```

### Event Count by Source IP

```spl
index=* source="security_incidents.csv"
| stats count by src_ip
```

### Events by Country

```spl
index=* source="security_incidents.csv"
| stats count by country
```

### Failed Logins

```spl
index=* source="security_incidents.csv" status=Failed
```

### Successful Logins

```spl
index=* source="security_incidents.csv" status=Success
```

### Privilege Escalation Events

```spl
index=* source="security_incidents.csv" event_type="Privilege Escalation"
```

### Severity Distribution

```spl
index=* source="security_incidents.csv"
| stats count by severity
```

---

## Findings

### User Analysis

The accounts **admin** and **root** generated the highest volume of suspicious activity.

### Authentication Analysis

Multiple failed login attempts were observed before successful authentication events.

### Country Analysis

The majority of suspicious events originated from foreign IP addresses associated with Russia and China.

### Source IP Analysis

Two IP addresses generated a significant concentration of failed login attempts and subsequent successful logins.

### Privilege Escalation

Privilege escalation events were successfully executed by:

* admin
* root

These events were classified as **Critical** severity.

---

## Risk Assessment

| Indicator                       | Risk     |
| ------------------------------- | -------- |
| Multiple Failed Logins          | High     |
| Successful Login After Failures | High     |
| Foreign Source IP Activity      | High     |
| Privilege Escalation            | Critical |

### Overall Risk Rating

**Critical**

---

## SOC Verdict

The investigation revealed several indicators commonly associated with account compromise:

* Repeated failed authentication attempts
* Successful logins following failures
* Suspicious foreign source IP activity
* Critical privilege escalation events

The combination of these indicators significantly increases the likelihood of malicious activity and warrants immediate investigation by the security team.

---

## Skills Demonstrated

* Splunk Log Analysis
* Authentication Monitoring
* Brute Force Detection
* Privilege Escalation Investigation
* Source IP Analysis
* Security Event Correlation
* Incident Documentation

---

## Key Takeaways

This investigation demonstrates how authentication logs, source IP information, and privilege escalation events can be correlated to identify potential account compromise and high-risk security incidents.

Monitoring failed logins, successful logins, and privilege escalation activity remains a critical SOC analyst responsibility.

---

## Repository Structure

Investigation-04-Security-Incident-Analysis/

* README.md
* security_incidents.csv
* screenshots/

---

**Author:** Hansal Savaliya

SOC Analyst | Splunk Learner | Cybersecurity Enthusiast
