###  **Other Important Services**
| Service | Purpose | SAA Exam Angle |
| ------ | ------ | ------ |
| CloudFormation | • Infrastructure as Code (IaC)<br>• Uses JSON/YAML templates | • Reproducible environments<br>• Drift detection<br>• Nested stacks for reusability<br>• StackSets for multi-account/region deployment |
| CDK (Cloud Development Kit) | • Software framework for IaC | • Uses TypeScript, Python, Java, C#, Go<br>• Compiles to CloudFormation<br>• Developer-friendly syntax and logic |
| Elastic Beanstalk | • Platform as a Service (PaaS)<br>• Automates app deployment | • Handles capacity, load balancing, auto-scaling, monitoring<br>• Retains control of EC2 underlying OS<br>• Best for standard, simple web architectures |
| OpsWorks | • Managed Chef/Puppet configuration | • Legacy; preserves existing configuration code<br>• Configures EC2 and on-premises environments |
| Systems Manager (SSM) | • Fleet operations management | • **Run Command**: Script execution without SSH<br>• **Patch Manager**: Automatic OS updates<br>• **Session Manager**: Shell access without port 22/bastions<br>• **Parameter Store**: Free config & secrets storage with KMS |
| AWS Organizations | • Centralized multi-account management | • **Service Control Policies (SCPs)**: Filter maximum permissions (member accounts)<br>• **Consolidated Billing**: Aggregated billing & volume discounts<br>• **OUs**: Hierarchical groupings |
| AWS Control Tower | • Orchestrator for multi-account landing zones | • Establishes guardrails (preventive SCPs, detective AWS Config)<br>• Account Factory automates compliant account creation |
| AWS Trusted Advisor | • Automated environment optimization advisor | • Analyzes 5 pillars: Cost, Performance, Security, Fault Tolerance, Limits<br>• Full checks require Business/Enterprise Support |
| AWS Cost Explorer | • Retrospective cost visualization tool | • Analyzes historical spending trends<br>• Provides forecasts and Savings Plans/RI recommendations |
| AWS Budgets | • Active cost governance and tracking tool | • Alerts when actual/forecasted cost/usage exceeds limits<br>• Can trigger automated actions (stop instances, apply SCPs) |
| Cognito | • User sign-up, sign-in, and access control | • **User Pools**: Directory, MFA, JWT auth<br>• **Identity Pools**: Exchanges tokens for temporary AWS credentials<br>• Often paired together |
| AWS Global Accelerator | • Networking performance accelerator | • Provides static Anycast IPs<br>• Routes via AWS backbone network to nearest healthy endpoint<br>• TCP/UDP non-HTTP support; fast failover<br>• *vs CloudFront*: CF caches static content; GA optimizes transport paths |
| AWS Outposts | • On-premises AWS infrastructure extension | • Runs native AWS services locally<br>• Best for sub-millisecond local latency or data residency requirements |
| AWS Wavelength | • Edge computing in telecom 5G networks | • Minimizes mobile latency by avoiding standard internet routing<br>• Connects mobile clients with ultra-low latency (<10ms) |
| AWS Local Zones | • Geo-specific metro infrastructure extension | • Runs compute, storage, databases near major metropolitan areas<br>• Achieves single-digit millisecond latency without on-prem hardware |
|
