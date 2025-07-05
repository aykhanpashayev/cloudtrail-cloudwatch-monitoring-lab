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
```
---

## 1️⃣ Task 1 — CloudTrail deep-dive & trail creation  

| Screenshot | What’s happening |
|------------|------------------|
| ![CloudTrail Event history](screenshots/Task1(1).png) | Inspecting the **Event history** to understand what data CloudTrail logs by default (90-day retention, management events only). |
| ![Create trail wizard](screenshots/Task1(2).png) | Launching the **Create trail** wizard – planned to store logs in S3 **and** stream to CloudWatch Logs for real-time analytics. |
| ![Trail permissions error](screenshots/Task1(3).png) | Lab role lacked permission to create a new trail with CWL integration, highlighting least-privilege best practice. |
| ![Existing managed trail](screenshots/Task1(4).png) | Discovered pre-configured trail `LabCloudTrail` that already streams to Log Group `CloudTrailLogGroup` – reused for the rest of the lab. |

### Key takeaway  
CloudTrail gives immutable audit logs; forwarding to CloudWatch enables near-real-time alerting without extra infrastructure.

---

## 2️⃣ Task 2 — SNS topic & email subscription  

| Screenshot | Description |
|------------|-------------|
| ![Create SNS topic](screenshots/Task2(1).png) | Creating **Standard topic** `MySNSTopic`; open publish/subscribe for lab speed. |
| ![Add email subscription](screenshots/Task2(2).png) | Subscribed personal inbox via **Email protocol** – will receive JSON notifications. |
| ![Subscription confirmed](screenshots/Task2(3).png) | Confirmation page after clicking link in AWS notification email. |

### Key takeaway  
SNS decouples producers (EventBridge, CloudWatch Alarms) from consumers (email, SMS, Lambda, etc.).

---

## 3️⃣ Task 3 — EventBridge rule → alert on security-group changes  

| Screenshot | Description |
|------------|-------------|
| ![Rule pattern JSON](screenshots/Task3(1).png) | Custom event pattern matching `AuthorizeSecurityGroupIngress` & `ModifyNetworkInterfaceAttribute`. |
| ![Input transformer](screenshots/Task3(2).png) | Input transformer injects SG ID, API name & request JSON into a readable message. |
| ![Editing SG](screenshots/Task3(3).png) | Added **SSH 0.0.0.0/0** to `LabSecurityGroup` to trigger the rule. |
| ![Email alert SG change](screenshots/Task3(4).png) | 📧 Received instant alert with full details – proves rule + SNS integration works. |

### Key takeaway  
EventBridge + CloudTrail lets you build fine-grained, real-time security detection without running servers.

---

## 4️⃣ Task 4 — CloudWatch metric filter & alarm for failed console log-ins  

| Screenshot | Description |
|------------|-------------|
| ![Metric filter definition](screenshots/Task4(1).png) | Filter pattern counts `ConsoleLogin` events where `errorMessage == "Failed authentication"`. |
| ![Alarm settings](screenshots/Task4(2).png) | Alarm `FailedLogins` fires when ≥ 3 failures in 5 minutes. |
| ![Generate failures](screenshots/Task4(3).png) | Intentionally failed sign-ins as user `test` three times. |
| ![Alarm email](screenshots/Task4(4).png) | 📧 Alarm transitioned to **ALARM** state; notification delivered via SNS. |

### Key takeaway  
Metric filters turn log patterns into quantitative metrics → alarms → automated response.

---

## 5️⃣ Task 5 — CloudWatch Logs Insights querying  

| Screenshot | Description |
|------------|-------------|
| ![Logs Insights query](screenshots/Task5(1).png) | Logs Insights query filters failed log-ins, groups by IP, reason, region, IAM ARN. |
| ![Query result](screenshots/Task5(2).png) | Results validate three failures from expected IP – handy for incident forensics. |

### Key takeaway  
Logs Insights is powerful for ad-hoc security hunts without external SIEM costs.

---

## 🔑 What I built & why it matters  

| Layer | Service(s) | Purpose |
|-------|------------|---------|
| **Audit log** | CloudTrail ➜ S3 & CloudWatch Logs | Complete record of every API call (who, what, when, where). |
| **Event routing** | Amazon EventBridge | Detects sensitive API calls & routes to targets. |
| **Notification** | Amazon SNS | Fan-out alerts (email; could add Slack, Lambda, PagerDuty…). |
| **Metrics & alarms** | CloudWatch Logs, Metric Filters, Alarms | Turns raw logs into actionable signals (e.g., brute-force detection). |
| **Ad-hoc analysis** | CloudWatch Logs Insights | Instant querying & visualization for investigations. |

Together these services deliver **serverless, pay-as-you-go security monitoring** across the AWS stack.

---

## 🚀 Next improvements

* Automatically remediate rogue SG changes via Lambda.  
* Centralize across accounts with AWS Organizations & Security Hub.  
* IaC-ify (CloudFormation/CDK) for production deployments.

---

## 📚 References

* [AWS CloudTrail](https://docs.aws.amazon.com/cloudtrail/)  
* [Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/)  
* [Amazon SNS](https://docs.aws.amazon.com/sns/)  
* [Amazon CloudWatch](https://docs.aws.amazon.com/cloudwatch/)  
* AWS Well-Architected – _Security Pillar_ (Logging & Monitoring)

> _Lab content © 2022 Amazon Web Services. Screenshots used under educational fair-use._

