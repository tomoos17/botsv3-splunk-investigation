# botsv3-splunk-investigation
Security Operations &amp; Incident Response investigation using Splunk and the BOTSv3 dataset. Coursework project for COMP3010 (University of Plymouth).

youtube video link: https://youtu.be/Z21MtkB8w2g?si=wH3-HhwoZoncZVt8

# BOTSv3 Splunk Investigation – Coursework Repository

Introduction

A Security Operations Centre (SOC) plays a central role in safeguarding an organisation’s information systems by continuously monitoring, detecting, analysing, and responding to potential cybersecurity threats. This investigation replicates the analytical workflow of a SOC environment by using Splunk Enterprise as a Security Information and Event Management (SIEM) platform. Splunk’s ability to aggregate, index, and correlate high-volume log data enables analysts to reconstruct events, identify anomalies, and derive actionable insights from diverse technical sources.

The dataset used for this work is the Boss of the SOC Version 3 (BOTSv3) dataset, developed by Splunk to simulate realistic enterprise security scenarios within a fictional company known as Frothly. BOTSv3 consists of pre-indexed logs from cloud services, endpoint devices, network activity, and authentication systems, with a strong emphasis on Amazon Web Services (AWS) activity. These logs are purposely designed to reflect misconfigurations, policy violations, and suspicious behaviours that security teams encounter in real operational environments.

The primary aim of this investigation is to analyse selected cloud-based security events, identify abnormal behaviours, and demonstrate how a SOC analyst would detect and interpret potential security risks using Splunk’s Search Processing Language (SPL). This report focuses specifically on questions 1–8 of the BOTSv3 guided question set, which relate to AWS IAM activity, multi-factor authentication (MFA) validation, public S3 bucket exposure, file uploads, and endpoint operating system inconsistencies. The scope of this investigation is limited to the data available within the BOTSv3 dataset and does not involve live incidents or dynamic threat response.

This report presents a structured analysis flow, including Splunk installation, dataset preparation, SOC role alignment, and detailed investigative findings. Screenshots, SPL queries, and analytical commentary are included to demonstrate both the technical process and the reasoning applied during the investigation.

SOC Roles & Incident Handling Reflection

A modern SOC is typically structured into tiered responsibility levels to ensure efficient monitoring and escalation of security incidents. Tier 1 analysts provide continuous monitoring of security alerts, log anomalies, and automated detections. Their role is to identify potential threats, validate whether alerts represent genuine incidents, and escalate them when necessary. Within this investigation, Tier 1 responsibilities are reflected in the detection of unusual AWS activity, identification of S3 permission changes, and recognition of events executed without multi-factor authentication.

Tier 2 analysts conduct deeper technical investigations. They correlate data across multiple systems, determine the root cause of an incident, assess the impact, and provide recommendations for containment. The BOTSv3 dataset mirrors these Tier 2 responsibilities through tasks such as identifying the user responsible for a misconfigured S3 bucket, analysing CloudTrail events to determine the extent of exposure, and cross-checking endpoint logs to identify system inconsistencies.

Tier 3 analysts, often specialising in digital forensics, threat hunting, malware analysis, and advanced incident response, handle complex threats and persistent attacks. While the guided questions do not require Tier 3 depth, the investigative thinking demonstrated aligns with foundational skills that feed into more advanced threat-hunting activities.

SOC workflows often align with structured incident-response frameworks, such as the NIST Incident Response Lifecycle, which consists of five major phases:

Preparation

Detection and Analysis

Containment

Eradication and Recovery

Post-Incident Activity

Each BOTSv3 question reinforces part of this lifecycle. For example:

Identifying IAM users accessing AWS aligns with Detection and Analysis.

Detecting API activity without MFA highlights weaknesses in Preparation and identity controls.

Discovering a publicly exposed S3 bucket aligns with Containment, as it reveals a misconfiguration requiring immediate correction.

Finding the specific text file uploaded to the exposed bucket aligns with Eradication and Recovery, as analysts determine what data was affected.

Overall, this investigation demonstrates how Splunk supports each phase of incident handling through centralised logging, correlation capabilities, and detailed search mechanisms. It highlights the structured approach SOC teams must adopt to analyse threats consistently and effectively.

Installation & Data Preparation

To replicate a realistic SOC investigation environment, Splunk Enterprise was installed on an Ubuntu virtual machine. Linux-based platforms are commonly used in security operations due to their stability, performance efficiency, and compatibility with enterprise SIEM tools. Installing Splunk in a virtualised environment also ensures isolation from the host system and reflects how SOC teams typically deploy SIEM solutions on dedicated servers.

Splunk Enterprise was downloaded from the official Splunk website and installed using the .deb package. After installation, Splunk was started through the terminal and accessed through the web interface at http://localhost:8000, where administrative credentials were configured. This interface provides access to search functionality, index management, dashboards, and data ingestion controls.

The BOTSv3 dataset was obtained from the official Splunk GitHub repository. BOTSv3 is packaged as a .tgz file containing Splunk-formatted index buckets (db, colddb, thaweddb). These were extracted and copied into Splunk’s indexing directory structure under a dedicated index named botsv3. This method allows Splunk to recognise and load the dataset instantly without requiring manual ingestion of individual log files.

After copying the dataset into the appropriate directories, file permissions were corrected using chown and chmod to ensure Splunk could read the data. Splunk was then restarted to apply the configuration changes and initialise the dataset.

## Repository Structure

```
queries/
   all_queries.spl

screenshots/
   q1.png
   q2.png
   q3.png
   q4.png
   q5.png
   q6.png
   q7.png
   q8.png

setup

README.md
```

## Summary of Findings

Guided Questions – Investigation & Analysis
This section presents the detailed analysis of Questions 1–8 from the BOTSv3 guided AWS-focused dataset. Each question includes the SPL query used, interpretation of results, and SOC relevance.

4.1 Question 1 – Identifying IAM Users Accessing AWS Services
Question:
List the IAM users that accessed an AWS service (successfully or unsuccessfully) in Frothly’s AWS environment.
SPL Query Used:
index=botsv3 sourcetype=aws:cloudtrail
| stats count by userIdentity.userName

Explanation:
AWS CloudTrail logs record every API event that occurs in the AWS environment. The field userIdentity.userName identifies the IAM user making the request. Using stats count provides an aggregated list of all IAM users appearing in logs.
 
Result:
The IAM users identified were:
bstoll, btun, splunk_access, web_admin

SOC Relevance:
This step aligns with Tier 1 SOC responsibilities—identifying active user accounts and verifying whether the activity is expected or suspicious. IAM activity analysis is essential for monitoring insider threats and validating policy compliance.
________________________________________
4.2 Question 2 – Detecting API Activity Without MFA
Question:
What field indicates AWS API activity without MFA? (Exclude console logins)

SPL Query Used:
index=botsv3 sourcetype=aws:cloudtrail "*mfaAuthenticated*" NOT "*ConsoleLogin*" 
| head 5

Explanation:
The field userIdentity.sessionContext.attributes.mfaAuthenticated indicates whether MFA was used (true or false) during API calls. Searching for this field directly reveals API actions that did not enforce MFA.
 
 Result:
userIdentity.sessionContext.attributes.mfaAuthenticated

SOC Relevance:
This reflects weak identity controls. Lack of MFA is a major security risk and often exploited in real attacks, placing this finding under the Detection and Preparation stages of the incident lifecycle.
________________________________________
4.3 Question 3 – Identifying Processor Used on Web Servers
Question:
What processor model is used by the Frothly web servers?

SPL Query Used:
index=botsv3 sourcetype=hardware

Explanation:
The hardware sourcetype contains system inventory information including CPU model, memory, and storage. Extracting processor values reveals the CPU type used across web hosts.
 
 Result:
E5-2676

SOC Relevance:
Understanding hardware baselines helps identify inconsistencies or unauthorised changes. If a single machine reports a different processor model, it may indicate tampering or misconfiguration.
________________________________________
4.4 Question 4 – Identifying the Event ID That Made an S3 Bucket Public
Question:
What is the Event ID of the API call that made the S3 bucket publicly accessible?

SPL Query Used:
index=botsv3 sourcetype=aws:cloudtrail eventName=PutBucketAcl "AllUsers"

Explanation:
PutBucketAcl is the API event used to modify S3 access control lists (ACLs). Searching for "AllUsers" reveals permissions that grant public access.
 
 Result:
ab45689d-69cd-41e7-8705-5350402cf7ac

SOC Relevance:
This represents a cloud misconfiguration—one of the leading causes of real-world breaches. Identifying the exact event and user responsible is essential for containment and compliance auditing.
________________________________________
4.5 Question 5 – Identifying the User Responsible for the S3 Misconfiguration
Question:
What is Bud’s username?

SPL Query Used:
index=botsv3 sourcetype=aws:cloudtrail eventName=PutBucketAcl "AllUsers"

Explanation:
This query extracts the username responsible for the public ACL change.
 
Result:
bstoll

SOC Relevance:
Attribution is critical in incident response. Identifying the user behind a risky change allows SOC analysts to determine intent—whether accidental (misconfiguration) or malicious (insider threat).
________________________________________
4.6 Question 6 – Identifying the Publicly Accessible S3 Bucket
Question:
What is the name of the S3 bucket that became publicly accessible?

SPL Query Used:
index=botsv3 sourcetype=aws:cloudtrail eventName=PutBucketAcl
| stats values(requestParameters.bucketName)

Explanation
To identify which S3 bucket was made publicly accessible, I filtered the CloudTrail logs for the PutBucketAcl event, which records changes to bucket permissions. The field requestParameters.bucketName revealed that the misconfiguration affected the bucket named frothlywebcode. This confirms that this bucket had its ACL updated in a way that allowed public access, exposing stored data to anyone on the internet.

Result:
frothlywebcode

SOC Relevance:
Knowing which bucket was exposed helps determine the scope of exposure and whether sensitive assets were placed at risk.
________________________________________
4.7 Question 7 – Identifying File Uploaded to the Public S3 Bucket
Question:
What text file was successfully uploaded while the bucket was exposed?

SPL Query Used:
index=botsv3 sourcetype="aws:s3:accesslogs" frothlywebcode "*.txt"

Explanation
To find the file uploaded while the bucket was publicly accessible, I examined the S3 access logs using the aws:s3:accesslogs sourcetype. Filtering by the bucket name showed a successful (HTTP 200) file upload request for OPEN_BUCKET_PLEASE_FIX.txt, which indicates that someone placed this file in the bucket—either as a warning about the misconfiguration or as proof that the bucket was writable by the public.
 
Result:
OPEN_BUCKET_PLEASE_FIX.txt

SOC Relevance:
This shows the bucket was publicly writable, not just readable—a far more severe risk. Anyone on the internet could upload or modify files.
________________________________________


4.8 Question 8 – Identifying Endpoint with Different OS Version
Question:
Which endpoint is running a different Windows edition than the others?

SPL Query Used:
1.	index=botsv3 winhostmon
   | stats values(os) by host

2. index=botsv3 “BSTOLL-L”

Explanation
Using the winhostmon sourcetype, I reviewed Windows host system information and compared OS versions across machines. One host, bstoll-l.froth.ly, was running a different Windows edition than the others, making it stand out as an anomaly. Systems with inconsistent OS configurations can indicate unmanaged devices, misconfigurations, or potential security risks in an enterprise environment. 
 

Result:
bstoll-l.froth.ly

SOC Relevance:
Inconsistent endpoint OS versions may indicate:
•	Misconfiguration
•	Unpatched systems
•	Shadow IT
•	Compromised hosts


```
queries/botsv3_queries.spl
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

**Thomas Gabriel Naduvilaveedu Martin**  
BSc Cyber Security  
University of Plymouth
