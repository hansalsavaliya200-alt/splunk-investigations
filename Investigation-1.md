# 🔐 Investigation 1 – Failed Login Analysis Using Splunk

## Overview

This investigation focuses on analyzing authentication logs to identify suspicious login activity. The objective was to determine which users and IP addresses generated the highest number of events and to identify potential indicators of brute-force attacks or unauthorized access attempts.

---

## Objectives

* Analyze login activity using Splunk.
* Identify users generating the highest number of events.
* Identify source IP addresses with the highest activity.
* Compare successful and failed login attempts.
* Determine whether any user accounts show suspicious behavior.

---

## Environment

**Tool Used:** Splunk Enterprise

**Data Source:** Authentication log dataset (CSV)

**Investigation Type:** Login Activity Analysis

---

## Splunk Queries Used

### Count Events by User

```spl
index=* | stats count by user
```

### Count Events by Source IP

```spl
index=* | stats count by source_ip
```

### Count Successful and Failed Logins

```spl
index=* | stats count by status
```

---

## Findings

### User Activity

| User  | Event Count             |
| ----- | ----------------------- |
| root  | Highest Activity        |
| john  | Successful Login        |
| admin | Login Activity Observed |

The **root** account generated the highest number of authentication events within the dataset.

---

### Source IP Analysis

| Source IP | Observation      |
| --------- | ---------------- |
| 10.0.0.5  | Highest Activity |

The source IP address **10.0.0.5** generated the largest number of events and should be reviewed for unusual behavior.

---

### Login Status Analysis

| Status  | Count |
| ------- | ----- |
| Failed  | 8     |
| Success | 2     |

Failed authentication attempts significantly exceeded successful login attempts.

---

## Security Analysis

The investigation identified multiple failed login attempts associated with the **root** account. A successful login was later observed following several failures.

This pattern may indicate:

* Password guessing attempts
* Brute-force activity
* Unauthorized access attempts
* User credential misuse

Although a successful login can be legitimate, repeated failures followed by success warrant additional investigation.

---

## Risk Assessment

**Affected Account:** root

**Affected IP:** 10.0.0.5

**Risk Level:** Medium

### Justification

* High number of failed authentication attempts.
* Successful login following repeated failures.
* Concentrated activity from a single IP address.

---

## Recommendations

1. Review authentication logs associated with the root account.
2. Verify whether the successful login was authorized.
3. Investigate activity originating from IP address 10.0.0.5.
4. Implement account lockout policies where appropriate.
5. Continue monitoring for repeated authentication failures.

---

## Conclusion

The analysis revealed suspicious authentication activity involving the root account. Multiple failed login attempts were followed by a successful login, which may indicate brute-force activity or unauthorized access attempts. Further investigation of the affected account and source IP address is recommended to determine whether the activity was legitimate or malicious.

---

### Skills Practiced

* Splunk Log Analysis
* Authentication Monitoring
* User Activity Analysis
* Source IP Investigation
* Security Event Correlation
* Incident Documentation
* SOC Analyst Methodology
