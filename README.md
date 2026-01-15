# botsv3-splunk-investigation
Security Operations &amp; Incident Response investigation using Splunk and the BOTSv3 dataset. Coursework project for COMP3010 (University of Plymouth).

# BOTSv3 Splunk Investigation – Coursework Repository

This repository contains all screenshots, SPL queries, and documentation used to complete the BOTSv3 (Boss of the SOC v3) investigation using Splunk Enterprise. The objective of this coursework was to analyse AWS CloudTrail, S3 Access Logs, and Windows Host Monitoring logs to identify anomalies, misconfigurations, and indicators of compromise.

## Overview

The investigation involved:

- Querying AWS CloudTrail logs to identify IAM activity  
- Detecting missing MFA authentication events  
- Investigating S3 bucket misconfiguration and public access  
- Identifying the event ID associated with PutBucketAcl  
- Extracting uploaded files from S3 access logs  
- Analyzing Windows host OS logs for anomalies  
- Identifying the endpoint with a different OS edition  

All analysis was performed using Splunk Search Processing Language (SPL).

## Repository Structure

```
screenshots/
   q1.png
   q2.png
   q3.png
   q4.png
   q5.png
   q6.png
   q7.png
   q8.png

splunk_queries/
   all_queries.spl

README.md
```

## Summary of Findings

### Question 1 – IAM Users Accessing AWS Services  
**Answer:** bstoll,btun,splunk_access,web_admin

### Question 2 – MFA Status Field (JSON Path)  
**Answer:** userIdentity.sessionContext.attributes.mfaAuthenticated

### Question 3 – Processor Number on Web Servers  
**Answer:** E5-2676

### Question 4 – Event ID of Public S3 ACL Change  
**Answer:** ab45689d-69cd-41e7-8705-5350402cf7ac

### Question 5 – Bud’s Username  
**Answer:** bstoll

### Question 6 – Public S3 Bucket Name  
**Answer:** frothlywebcode

### Question 7 – Uploaded Text File Name  
**Answer:** OPEN_BUCKET_PLEASE_FIX.txt

### Question 8 – FQDN of Endpoint with Different Windows OS  
**Answer:** BSTOLL-L.froth.ly

## SPL Queries Used

All SPL queries used in this investigation are stored in:

```
queries/all_queries.spl
```

These include:

- IAM enumeration  
- MFA verification search  
- S3 ACL change detection  
- Event ID extraction  
- S3 object upload identification  
- OS anomaly detection  

## Tools & Technologies

| Tool | Purpose |
|------|---------|
| Splunk Enterprise | SIEM log search & investigation |
| BOTSv3 Dataset | Realistic SOC exercise |
| AWS CloudTrail Logs | IAM and S3 API activity |
| AWS S3 Access Logs | File upload/download analysis |
| WinHostMon | Windows host OS monitoring |
| WinEventLog | Security log investigation |
| Ubuntu VM | Splunk environment |
| GitHub | Documentation & submission |

## Skills Demonstrated

- Security log analysis  
- Threat hunting  
- AWS cloud security investigation  
- IAM audit and monitoring  
- S3 ACL misconfiguration detection  
- Splunk SPL querying  
- Host-based anomaly detection  
- Incident response workflow documentation  

## Author

**Thomas Naduvilaveedu Martin**  
BSc Cyber Security  
University of Plymouth
