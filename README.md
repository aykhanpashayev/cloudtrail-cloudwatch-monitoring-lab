# Lab 6.1 — Monitoring & Alerting with **AWS CloudTrail**, **CloudWatch**, **SNS** & **EventBridge**  

---

## ✨ Why this repo exists
Modern cloud workloads **must** be observable and auditable.  
In this guided lab I built a full-stack monitoring & alerting pipeline that

* **Logs** every API call with CloudTrail  
* **Streams** those logs to CloudWatch Logs  
* **Detects & Alerts** on security-sensitive activity (e.g. security-group changes) via EventBridge + SNS  
* **Raises Alarms** on anomalous behaviour (multiple failed console log-ins) with CloudWatch Metric Filters & Alarms  
* **Queries** historical logs with CloudWatch Logs Insights  

This repository contains

* All **screenshots** captured while completing the lab (`/screenshots`)  
* High-level **architecture diagrams** (`/architecture`)  
* A step-by-step **walk-through** of what was built, why it matters, and the results  

Feel free to clone / fork if you want to replicate the setup or use the README as inspiration for your own projects.

---

## 🗺️ High-level architecture

| After Task 3 | After Task 5 |
|:------------:|:-----------:|
| ![Architecture after Task 3](architecture/end-task-3.png) | ![Architecture after Task 5](architecture/end-task-5.png) |

---

## 📂 Repo structure
```text
.
├── architecture
│   ├── end-task-3.png
│   └── end-task-5.png
├── screenshots
│   ├── Task1(1).png … Task1(4).png   # CloudTrail & trail creation
│   ├── Task2(1).png … Task2(3).png   # SNS topic + subscription
│   ├── Task3(1).png … Task3(4).png   # EventBridge rule & email alert
│   ├── Task4(1).png … Task4(4).png   # Metric filter, alarm, failed log-ins
│   └── Task5(1).png … Task5(2).png   # Logs Insights query
└── README.md

1 Lab setup
Screenshot	What’s happening
Pre-existing CloudTrail event history before any custom trail was added
Attempting to create a new trail (MyLabCloudTrail) with CloudWatch Logs integration
Trail creation was restricted in the lab role, but a managed trail LabCloudTrail already captures & forwards management events
Trail properties – shows S3 bucket and CloudWatch Log Group destinations

2 Simple Notification Service (SNS)
Screenshot	What’s happening
Creating a standard SNS topic MySNSTopic and allowing “Everyone” to publish/subscribe (lab convenience)
Adding an email subscription so I receive alerts in my inbox
Confirmation page after clicking the link in the confirmation email

3 EventBridge rule – security-group change alerts
Screenshot	What’s happening
Custom Event Pattern – fires on AuthorizeSecurityGroupIngress or ModifyNetworkInterfaceAttribute API calls
Target configured as MySNSTopic with Input Transformer to craft a human-readable email message
Modifying the LabSecurityGroup inbound rules (adding SSH from Anywhere) to trigger the rule
✅ Received real-time email alert detailing the API call, SG ID, timestamp, and full request parameters

4 CloudWatch Metric Filter & Alarm – failed console log-ins
Screenshot	What’s happening
Metric Filter ConsoleLoginErrors detects ConsoleLogin events with Failed authentication
CloudWatch Alarm FailedLogins triggers when ≥ 3 failures occur within 5 minutes
Intentionally logging in as user test with wrong password three times
📧 Alarm transitioned to ALARM state – email received with full JSON payload for automation hooks

5 CloudWatch Logs Insights – ad-hoc security hunting
Screenshot	What’s happening
Query filtering failed console log-ins and grouping by source IP, region, reason, and IAM ARN
Query output confirms three failures from the expected IP and user – useful for forensics & dashboards

🔑 What I learned
Concept	Key takeaway
CloudTrail	Creates an immutable, 90-day event history by default; extending retention & adding data events requires a trail
Trail → CloudWatch Logs	Streaming events enables near real-time analytics, metric filters, and automated responses
EventBridge	Acts as the central event bus – build least-privilege rules that pipe only the signal you care about to downstream consumers
SNS	Quick fan-out; email is handy for demos, but the same topic can trigger Lambda, Slack webhooks, etc.
Metric Filters / Alarms	Turn log patterns into numeric metrics → thresholds → alerts or auto-remediation
Logs Insights	SQL-like syntax, useful for one-off hunts without shipping logs to external SIEM
Security posture	Combining these services lets small teams achieve enterprise-grade visibility without managing servers

📝 Next steps
Wire SNS → Lambda for automatic remediation (e.g. revert rogue SG edits)

Ship CloudTrail & VPC Flow Logs to an S3 data lake + Athena for long-term analytics

Integrate with AWS Security Hub to centralize findings across accounts/regions

Parameterize with IaC (CloudFormation / CDK / Terraform) for reproducible deployment

📚 References
AWS CloudTrail User Guide

Amazon CloudWatch Logs Insights – Query Syntax

Amazon EventBridge User Guide

AWS Well-Architected – Logging & Monitoring Best Practices
