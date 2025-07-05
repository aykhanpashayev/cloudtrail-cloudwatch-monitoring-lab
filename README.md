# AWS Monitoring & Alerting Lab: CloudTrail, CloudWatch, EventBridge, and SNS

## 🧠 Objective

This project demonstrates how to implement centralized logging, monitoring, and alerting on AWS using:

- AWS CloudTrail
- Amazon CloudWatch
- Amazon SNS (Simple Notification Service)
- Amazon EventBridge

By the end of this lab, the system was configured to:
- Track API activity with CloudTrail
- Send email alerts on security group changes using EventBridge + SNS
- Trigger CloudWatch alarms for multiple failed login attempts
- Query failed login attempts using CloudWatch Logs Insights

## 🛠️ AWS Services Used

- **AWS CloudTrail**: Tracks API calls across the AWS account.
- **Amazon CloudWatch**: Collects metrics, creates alarms, and stores log data.
- **Amazon SNS**: Sends notifications (email alerts).
- **Amazon EventBridge**: Detects specific API calls and triggers notifications.
- **AWS IAM**: For user identity and login testing.
- **Amazon EC2**: Used to simulate a security group modification event.

---

## ✅ Task Breakdown

### Task 1: CloudTrail Configuration
- Analyzed recent CloudTrail events (e.g., `CreateStack` from CloudFormation).
- Identified key fields: `eventTime`, `userIdentity`, `eventSource`.
- Reviewed an existing CloudTrail trail configured to send logs to CloudWatch Logs.

### Task 2: SNS Topic & Subscription
- Created an SNS topic named `MySNSTopic`.
- Subscribed an email address to the topic.
- (Note: Email delivery issue was observed, but subscription was confirmed.)

### Task 3: EventBridge Rule for Security Group Changes
- Created a rule to detect changes like `AuthorizeSecurityGroupIngress`.
- Configured it to trigger an SNS notification.
- Modified a security group to test event detection.
- Verified rule execution and CloudTrail event details.

### Task 4: CloudWatch Alarm for Failed Logins
- Created a metric filter for `ConsoleLogin` failures from CloudTrail logs.
- Created an alarm `FailedLogins` to trigger on 3+ failed login attempts within 5 minutes.
- Tested login failures via IAM user and confirmed alarm activation.

### Task 5: CloudWatch Logs Insights Query
- Queried `CloudTrailLogGroup` using CloudWatch Logs Insights to identify failed login sources.
- Used a custom query to count failures by IP, region, error type, and user.

