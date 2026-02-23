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

#



## 👤 Author

**Olusegun Fajobi**
Cybersecurity Engineer (Blue & Red Team)
GitHub: [https://github.com/samfajobi](https://github.com/samfajobi)
