# 🔐 Investigation 3 – Authentication Activity Investigation Using Splunk

## Overview

This investigation focused on analyzing authentication logs to identify suspicious user activity, failed login attempts, and potentially compromised accounts. The objective was to determine which users generated the most activity, identify suspicious source IP addresses, and assess whether any authentication patterns indicated malicious behavior.

---

## Objectives

* Analyze authentication events.
* Identify users with the highest activity.
* Determine the most active source IP address.
* Compare successful and failed login attempts.
* Identify accounts requiring further investigation.

---

## Environment

**Tool Used:** Splunk Enterprise

**Data Source:** Authentication Log Dataset (CSV)

**Investigation Type:** User Authentication Analysis

---

## Investigation Questions

### 1. Which user generated the most events?

### 2. Which source IP address generated the most activity?

### 3. How many successful and failed logins occurred?

### 4. Which accounts appear suspicious?

### 5. Which account should be investigated first?

---

## Splunk Queries Used

### Count Events by User

```spl
index=* | stats count by user
```

### Count Events by Source IP

```spl
index=* | stats count by src_ip
```

### Count Events by Status

```spl
index=* | stats count by status
```

---

## Findings

### User Activity Analysis

| User  | Event Count |
| ----- | ----------- |
| root  | 5           |
| admin | 4           |
| test  | 3           |

The **root** account generated the highest number of events in the dataset.

---

### Source IP Analysis

| Source IP   | Observation               |
| ----------- | ------------------------- |
| 77.88.99.10 | Highest activity observed |

The IP address **77.88.99.10** generated the largest number of authentication events.

---

### Authentication Status Analysis

| Status  | Count |
| ------- | ----- |
| Failed  | 9     |
| Success | 5     |

Failed login attempts exceeded successful logins.

---

## Security Analysis

Several user accounts displayed suspicious authentication activity.

### Root Account

* Generated the highest number of events.
* Multiple failed authentication attempts observed.
* High concentration of activity compared to other users.

### Admin Account

* Generated multiple authentication events.
* Failed login attempts observed.
* Privileged account requiring additional monitoring.

### Test Account

* Lower activity than root and admin.
* Still generated multiple failed login attempts.

---

## Risk Assessment

### Primary Account of Interest

**root**

### Secondary Account of Interest

**admin**

### Suspicious Source IP

**77.88.99.10**

### Risk Level

**Medium to High**

### Justification

* Multiple failed login attempts.
* Activity concentrated around privileged accounts.
* Elevated event volume from a single source IP address.

---

## Recommendations

1. Review authentication logs for root and admin accounts.
2. Verify whether login activity was authorized.
3. Monitor future activity from 77.88.99.10.
4. Implement account lockout policies where appropriate.
5. Continue monitoring authentication failures.

---

## Conclusion

The investigation identified elevated authentication activity involving privileged accounts, particularly the root account. Failed login attempts significantly exceeded successful logins, and source IP 77.88.99.10 generated the highest activity. Continued monitoring and validation of privileged account access are recommended.

---

## Skills Practiced

* Splunk Log Analysis
* Authentication Monitoring
* User Activity Analysis
* Source IP Investigation
* Failed Login Analysis
* Security Event Correlation
* SOC Analyst Methodology
