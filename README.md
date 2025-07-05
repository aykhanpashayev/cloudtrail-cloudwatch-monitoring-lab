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
