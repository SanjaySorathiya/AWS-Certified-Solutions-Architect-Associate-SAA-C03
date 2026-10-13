### **Monitoring Stack - CW, CT, Config**

| Service / Component | What it Monitors & Key Capabilities | What it Stores & Retention | SAA-C03 Exam Trigger Words & Scenarios |
| :--- | :--- | :--- | :--- |
| **CW Metrics** | • Performance metrics (CPU, disk, network, memory)<br>• Basic Monitoring (5-min intervals, free)<br>• Detailed Monitoring (1-min intervals, paid) | • 15 months retention by default<br>• High-resolution custom metrics (down to 1-sec, paid) | • "CPU utilization"<br>• "detailed monitoring (1-min)"<br>• "custom metric (memory/disk)"<br>• "billing alarm" |
| **CW Logs** | • Ingests logs from EC2 (needs CW Agent), Lambda, RDS, VPC Flow Logs, Route 53<br>• Metric Filters parse logs to trigger alarms<br>• Logs Insights queries logs via SQL-like syntax | • Retention configurable per log group (1 day to 10 years, or never expire)<br>• Encrypted via KMS<br>• Streams to S3, Kinesis, or OpenSearch | • "centralize logs"<br>• "filter log events"<br>• "SQL-like querying"<br>• "CW Agent on EC2" |
| **CW Alarms** | • Evaluates metrics over defined periods<br>• 3 States: `OK`, `ALARM`, `INSUFFICIENT_DATA`<br>• Composite alarms combine multiple conditions | • Triggers automated actions:<br>  - SNS notifications<br>  - ASG scaling policies<br>  - EC2 actions (stop, terminate, reboot, recover)<br>  - SSM Automation | • "notify via SNS when CPU > 80%"<br>• "trigger ASG scaling"<br>• "EC2 auto-recovery" |
| **EventBridge (CW Events)** | • Near-real-time resource state changes<br>• Matches event patterns or cron/rate schedules | • Routes JSON payloads to targets (Lambda, SQS, SNS, Step Functions, SSM) | • "trigger Lambda on schedule"<br>• "react to EC2 state change"<br>• "cron pattern" |
| **CW Synthetics** | • Endpoints & APIs 24/7 availability<br>• Canaries mimic customer traffic & monitor workflows | • Stores screenshots, HAR files, logs, & history in S3 | • "simulate user behavior"<br>• "monitor API 24/7"<br>• "canary script" |
| **CloudTrail (CT)** | • Account governance, compliance, and user API activity<br>• Management Events (free) vs. Data Events (S3 objects, Lambda runs, paid) | • 90-day Event History (free)<br>• Permanent trails saved to S3 (supports KMS & Log File Integrity Validation) or CW Logs | • "audit API calls"<br>• "who deleted S3 bucket"<br>• "organizational trail" |
| **AWS Config** | • Configuration history, state, and relationship mapping of resources<br>• Drift monitoring against Config Rules | • Saves Configuration Items (CIs) to S3<br>• Tracks active config history (up to 7 years) | • "resource compliance"<br>• "track SG drift"<br>• "remediate non-compliant configurations" |
| **AWS X-Ray** | • Distributed tracing for microservices (ALB, API Gateway, EC2, ECS, Lambda, DynamoDB, RDS)<br>• Maps call paths & identifies latency | • Generates visual Service Maps, tracks exceptions, & logs HTTP status codes | • "distributed tracing"<br>• "find bottleneck"<br>• "debug HTTP 5xx errors" |
| **AWS Health Dashboard** | • Service-related events affecting your account (maintenance, outages, degradation) | • Integrates with EventBridge for automated remediations | • "scheduled EC2 maintenance"<br>• "AWS service outage alert" |

---

### **Deep-Dive Governance & Audit Comparison: CW vs. CT vs. Config**

| Dimension | CloudWatch (CW) | AWS CloudTrail (CT) | AWS Config |
| :--- | :--- | :--- | :--- |
| **Primary Objective** | • **Performance & Operational Health** | • **API Activity & User Auditing** | • **Configuration State & Compliance** |
| **Core Question** | • *"How is my system/app performing?"* | • *"Who made the API call to change this resource?"* | • *"Is this resource compliant, and what was its history?"* |
| **Data Type** | • Numerical metrics & text log streams | • JSON-formatted API event records | • JSON-based Configuration Items (CIs) |
| **Key SAA-C03 Use Cases** | • ASG scaling<br>• Resource utilization metrics<br>• Log analysis<br>• Billing alerts | • Security forensics<br>• Compliance audits<br>• IAM privilege changes tracking | • Configuration drift tracking<br>• Compliance rule enforcement<br>• Change management |
| **Storage / Retention** | • Logs: Configurable (1 day to 10 yrs)<br>• Metrics: 15 months | • 90 days Event History (free)<br>• Infinite via S3 | • Infinite via S3<br>• Active state retained 7 years |
| **Automation Pathways** | • Alarms trigger ASG scaling, SNS, or EC2 recovery | • EventBridge rule captures CT API call to launch Lambda/SQS | • Auto-remediates non-compliant rules via SSM Automation or SNS |
| **Multi-Account Scale** | • Cross-account/cross-region dashboards | • Organizational Trails | • Multi-account compliance via Config Aggregators |
| **Security Scenario** | • Alert when unauthorized login attempts exceed threshold | • Identify the exact IAM user who modified an SG rule | • Detect and flag an SG open to public (`0.0.0.0/0` on Port 22) |

---

### **⚠️ SAA-C03 EXAM TRAPS: CW vs. CT vs. Config**

*   **Who exposed the S3 bucket?** -> Use **CT** (tracks the API event that updated the bucket policy).
*   **Is the S3 bucket currently exposed?** -> Use **Config** (evaluates policy against compliance rules like `s3-bucket-public-read-prohibited`).
*   **How much data is being downloaded from the bucket?** -> Use **CW Metrics** (tracks S3 performance metrics and byte counts).
*   **Detailed Monitoring vs. Basic Monitoring**:
    *   *Basic*: Free, runs at 5-minute intervals.
    *   *Detailed*: Paid, runs at 1-minute intervals. Required for near-real-time monitoring with 1-minute granularity.
*   **CW Agent**: By default, EC2 does NOT send memory utilization, disk space, or guest OS logs to CW. You *must* install the unified CloudWatch Agent on the guest OS to collect these guest-level metrics.

---
