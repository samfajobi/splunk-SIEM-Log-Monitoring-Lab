# Splunk Log Analysis Project

## Overview

This project demonstrates hands-on log analysis using Splunk across multiple log sources such as DNS logs, HTTP/HTTPS logs, and Windows Event Logs.

The objective of this project is to simulate real-world SOC analyst workflows by ingesting, analyzing, correlating, and detecting suspicious activities using Splunk’s Search Processing Language (SPL).

This repository highlights practical security monitoring, detection engineering, and investigative skills.

---

## Project Goals

* Understand different log types and structures
* Perform log ingestion and parsing in Splunk
* Write effective SPL queries
* Detect suspicious and malicious behavior
* Correlate events across multiple log sources
* Document investigations clearly and professionally

---

## Environment Setup

### Platform

* Splunk Enterprise (Local Lab Setup)
* Ubuntu / Windows Log Sources
* Sample DNS and Web Logs

### Data Ingestion Methods

* File Upload (Add Data)
* Universal Forwarder (where applicable)
* Custom indexes per log type

Example index structure:

```
index=dns_logs
index=web_logs
index=windows_logs
```

---

## Log Sources Analyzed

---

# 1. DNS Logs

## Purpose

DNS logs help monitor domain resolution requests and identify suspicious domain activity.

## Key Fields

* `query`
* `src_ip`
* `dest_ip`
* `record_type`
* `response_code`

## Security Use Cases

* Detect DNS tunneling
* Identify communication with malicious domains
* Monitor high NXDOMAIN responses
* Detect beaconing behavior
* Identify suspicious TLD usage

## Example SPL Queries

### Top Queried Domains

```spl
index=dns_logs
| stats count by query
| sort - count
```

### Excessive NXDOMAIN Responses

```spl
index=dns_logs response_code=NXDOMAIN
| stats count by src_ip
| sort - count
```

### Detect Potential DNS Tunneling (Long Queries)

```spl
index=dns_logs
| eval query_length=len(query)
| where query_length > 50
| table _time src_ip query query_length
```

---

# 2. HTTP / HTTPS Logs

## Purpose

Web logs provide visibility into user browsing behavior and potential malicious web activity.

## Key Fields

* `src_ip`
* `dest_ip`
* `uri`
* `http_method`
* `status_code`
* `user_agent`

## Security Use Cases

* Detect access to malicious domains
* Identify data exfiltration patterns
* Detect suspicious user agents
* Monitor unusual POST requests
* Identify web scanning activity

## Example SPL Queries

### Top Visited URLs

```spl
index=web_logs
| stats count by uri
| sort - count
```

### Suspicious HTTP Methods

```spl
index=web_logs http_method=POST
| stats count by src_ip uri
| sort - count
```

### Detect Web Scanning Behavior

```spl
index=web_logs
| stats dc(uri) as unique_urls by src_ip
| where unique_urls > 50
| sort - unique_urls
```

### Suspicious User Agents

```spl
index=web_logs
| stats count by user_agent
| sort - count
```

---

# 3. Windows Event Logs

## Purpose

Windows Event Logs provide critical endpoint visibility for authentication, process execution, and system changes.

## Key Log Types

* Security Logs
* System Logs
* Application Logs

## Important Event IDs

* 4624 – Successful logon
* 4625 – Failed logon
* 4688 – Process creation
* 4720 – User account created
* 4726 – User account deleted
* 4740 – Account locked out

## Security Use Cases

* Detect brute force attacks
* Identify privilege escalation
* Monitor suspicious process execution
* Detect unauthorized account creation
* Investigate lateral movement

## Example SPL Queries

### Failed Logon Attempts

```spl
index=windows_logs EventCode=4625
| stats count by Account_Name src_ip
| sort - count
```

### Successful Logons

```spl
index=windows_logs EventCode=4624
| stats count by Account_Name Logon_Type
```

### Process Creation Monitoring

```spl
index=windows_logs EventCode=4688
| stats count by New_Process_Name Parent_Process_Name
| sort - count
```

### Account Lockouts

```spl
index=windows_logs EventCode=4740
| table _time Account_Name Caller_Computer_Name
```

---

# Correlation Examples

## Detect Possible Brute Force Followed by Success

```spl
index=windows_logs (EventCode=4624 OR EventCode=4625)
| stats count(eval(EventCode=4625)) as failed_attempts
        count(eval(EventCode=4624)) as successful_logins
        by Account_Name src_ip
| where failed_attempts > 5 AND successful_logins > 0
```

## DNS + Web Correlation

```spl
(index=dns_logs OR index=web_logs)
| stats values(query) values(uri) by src_ip
```

---

# Dashboards Created

* DNS Activity Overview
* Web Traffic Monitoring Dashboard
* Windows Authentication Monitoring
* Suspicious Activity Overview
* Brute Force Detection Panel

Each dashboard includes:

* Top talkers
* Event trends over time
* Suspicious behavior indicators
* Drill-down investigation panels

---

# Alerting Scenarios

Configured alerts for:

* Multiple failed logins within 5 minutes
* High volume DNS queries from single host
* Suspicious process execution
* Excessive POST requests
* Account lockouts

Example Alert Query:

```spl
index=windows_logs EventCode=4625
| stats count by src_ip
| where count > 10
```

---

# Key Skills Demonstrated

* Log parsing and normalization
* SPL query writing
* Threat detection logic
* Event correlation
* Security investigation workflow
* Dashboard creation
* Alert configuration

---

# Project Structure

```
splunk-log-analysis/
│
├── dns-analysis.md
├── web-analysis.md
├── windows-event-analysis.md
├── dashboards/
├── alerts/
└── screenshots/
```

---

# Lessons Learned

* Different log sources provide different visibility layers
* Correlation improves detection accuracy
* Log normalization is critical for effective searching
* Small anomalies often indicate larger security issues
* Documentation improves investigation clarity

---

# Conclusion

This project simulates real-world SOC analyst responsibilities by analyzing multiple log sources using Splunk. It demonstrates the ability to detect suspicious behavior, correlate events, build dashboards, and create meaningful alerts.

The project reflects practical security monitoring and detection engineering skills suitable for junior SOC analyst or blue team roles.

---

## 👤 Author

**Olusegun Fajobi**
Cybersecurity Engineer (Blue & Red Team)
GitHub: [https://github.com/samfajobi](https://github.com/samfajobi)
