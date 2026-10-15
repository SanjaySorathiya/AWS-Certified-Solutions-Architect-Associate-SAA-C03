# **Core Architectural Patterns for SAA**

## **1\. Highly Available Web Application**

This pattern appears in many SAA exam scenarios. Know it cold.

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| DNS | Amazon Route 53 (Latency/Failover) | Health checks; route to nearest/healthy endpoint |
| CDN | Amazon CloudFront | Cache content; reduce origin load; TLS at edge |
| Load Balancer | Application Load Balancer (ALB) | Multi-AZ; path routing; health checks |
| Web Tier | Amazon EC2 + Auto Scaling (or ECS/Fargate) | Horizontal scaling; AZ fault tolerance |
| App Tier | EC2/Lambda in private subnets | Isolation; scalable compute |
| Cache | Amazon ElastiCache | Reduce DB reads; cache/session data |
| Database | Amazon Aurora/RDS Multi-AZ + read replicas | HA for writes; scale reads |
| Storage | Amazon S3 | Durable, scalable object storage |
| Secrets | AWS Secrets Manager / Systems Manager Parameter Store | Avoid hardcoded credentials |
| Network | Amazon VPC + public/private subnets + NAT Gateway | Isolation; private-tier outbound access |

## **2\. Static Website**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Storage | Amazon S3 | Host static assets |
| CDN | Amazon CloudFront | Global low-latency delivery; caching |
| DNS | Amazon Route 53 | Custom domain; DNS routing |

**Choose when:** Static HTML/CSS/JS; no server-side compute required.

## **3\. Serverless Web Application Pattern**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| CDN | Amazon CloudFront | Global delivery for SPA/static assets |
| API | Amazon API Gateway | Managed HTTP/REST API entry point |
| Compute | AWS Lambda | Serverless, stateless compute |
| Database | Amazon DynamoDB | Serverless NoSQL; automatic scaling |
| Storage | Amazon S3 | Files/static content; event triggers |
| Auth | Amazon Cognito | User authentication and AWS credentials |

## **4\. REST API Backend**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| API | Amazon API Gateway | Managed REST API |
| Compute | AWS Lambda | Serverless backend |
| Database | Amazon DynamoDB | Serverless NoSQL backend |

**Choose when:** Mobile/web clients need a managed API backend.

| 💡 TIP | API Gateway choice |
| ----- | ----- |
| HTTP API | Lower-cost, lower-feature option; use when advanced REST API features are unnecessary |
| REST API | Use when features such as usage plans/API keys or built-in WAF support are required |

## **5\. Microservices Architecture**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Entry | ALB | Route requests to services |
| Compute | Amazon ECS/Fargate | Containerized microservices without server management |
| Data | Amazon RDS/Aurora or DynamoDB | Relational or NoSQL data per service |
| Messaging | Amazon SQS/SNS | Decouple services; async communication |

## **6\. Container Platform**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Registry | Amazon ECR | Store container images |
| Compute | Amazon ECS/EKS + Fargate | Run containers; Fargate removes server management |
| Load Balancer | ALB | Distribute HTTP/HTTPS traffic |

**Choose when:** Docker/container workloads; choose ECS for simpler AWS-native orchestration, EKS for Kubernetes.

## **7\. Decoupled Architecture**

**Pattern:** Producer → queue → worker → database. Components operate independently.

| Scenario | Pattern | Services |
| ----- | ----- | ----- |
| Smooth traffic spikes; retry failures | Queue + Worker | Amazon SQS → EC2 Auto Scaling/Lambda → DynamoDB/RDS |
| Prevent producer from waiting for consumer | Async messaging | SQS |
| Failed messages need isolation | Dead-letter queue | SQS + DLQ |

## **8\. Fan-Out Messaging**

**Pattern:** One publisher → multiple independent consumers.

| Scenario | Pattern | Services |
| ----- | ----- | ----- |
| One event must reach multiple services | Fan-out | Amazon SNS → multiple SQS queues |
| Independent processing/retry | Durable fan-out | SNS → SQS → consumers |
| Multiple subscribers need same event | Pub/sub | SNS |

## **9\. Event-Driven Architecture**

**Pattern:** Event producer → event bus/rule → target.

| Scenario | Pattern | Services |
| ----- | ----- | ----- |
| React to AWS/service events | Event routing | Amazon EventBridge → Lambda/SQS/SNS |
| Route events by content | Rules | EventBridge event pattern |
| Scheduled event processing | Scheduler | EventBridge Scheduler → target |

## **10\. Real-Time Data Streaming**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Stream | Amazon Kinesis Data Streams | Real-time ingestion |
| Processing | AWS Lambda | Event-driven processing |
| Storage | Amazon S3 / DynamoDB | Data lake or low-latency access |

**Choose when:** IoT telemetry, clickstreams, logs, or continuously generated data require near-real-time processing.

## **11\. Content Delivery Network (CDN)**

| Origin | Service(s) | Why |
| ----- | ----- | ----- |
| Static content | Amazon CloudFront → S3 | Cache objects globally |
| Dynamic/web app | CloudFront → ALB | Reduce latency; protect/origin offload |
| API | CloudFront → API Gateway | Edge caching where appropriate |

**Choose when:** Users are geographically distributed and latency must be reduced.

## **12\. Private EC2 Internet Access**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Compute | EC2 in private subnet | No direct inbound internet exposure |
| Egress | NAT Gateway in public subnet | Private instances initiate outbound connections |
| Internet | Internet Gateway | NAT Gateway's internet path |

**Exam clue:** Private EC2 needs software updates/package downloads → NAT Gateway.

## **13\. Secure Private AWS Service Access**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Compute | EC2 in private subnet | No public IP required |
| Endpoint | VPC Gateway Endpoint | Private access to S3/DynamoDB |
| Routing | Route table | Send service traffic to endpoint |

**Exam clue:** Private subnet → S3/DynamoDB without NAT/Internet Gateway → Gateway VPC endpoint.

## **14\. Hybrid Network**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| On-premises | Corporate network | Existing workloads/data |
| Connection | AWS Site-to-Site VPN | Encrypted connectivity over internet |
| Dedicated option | AWS Direct Connect | Private, consistent network connection |
| AWS | Amazon VPC | Cloud-side network |

**Choose:** VPN for fast/low-cost setup; Direct Connect for dedicated, consistent connectivity.

## **15\. Multi-VPC Enterprise Network**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Hub | AWS Transit Gateway | Central routing hub |
| Spokes | Multiple VPCs | Connect many networks |
| Hybrid | VPN/Direct Connect | Central on-premises connectivity |

**Choose when:** Many VPCs/accounts require centralized connectivity instead of many point-to-point links.

## **16\. Shared File System**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Compute | Multiple Amazon EC2 instances | Concurrent access |
| File System | Amazon EFS | Managed, elastic shared file storage |
| Network | VPC | Mount targets for access |

**Choose when:** Multiple Linux workloads need shared files.

## **17\. Windows File Server**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Compute | Windows EC2/workloads | Windows application access |
| File System | Amazon FSx for Windows File Server | Managed Windows file shares |
| Protocol | SMB | Windows-native file sharing |

**Choose when:** Windows/SMB, Active Directory integration, or Windows-compatible shared storage is required.

## **18\. High-Performance Relational Database**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Application | EC2/ECS/Lambda | Application workload |
| Database | Amazon Aurora | Managed, high-performance relational database |
| Read scale | Aurora Replicas | Scale read-heavy workloads |

**Exam clue:** Relational + high performance + read-heavy → Aurora + read replicas.

## **19\. Caching Layer**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Application | EC2/ECS/Lambda | Application requests |
| Cache | Amazon ElastiCache | Low-latency cached data |
| Database | RDS/Aurora | Persistent source of truth |

**Choose when:** Repeated reads cause database load or latency.

## **20\. Backup & Archive**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Primary storage | Amazon S3 | Durable object storage |
| Archive | S3 Glacier storage classes | Low-cost long-term retention |
| Protection | AWS Backup | Centralized backup management for supported resources |

**Choose when:** Data is infrequently accessed and must be retained for long periods.

## **21\. Cross-Region Disaster Recovery**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| DNS | Amazon Route 53 Failover routing | Direct traffic to healthy Region |
| Database | Aurora Global Database / RDS cross-Region read replica | Cross-Region data replication |
| Objects | Amazon S3 Cross-Region Replication | Replicate objects to another Region |

**Choose when:** Regional failure requires recovery in another Region.

## **22\. Monitoring & Auditing**

| Need | Service(s) | Why |
| ----- | ----- | ----- |
| Metrics/logs/alarms | Amazon CloudWatch | Operational monitoring |
| API/activity audit | AWS CloudTrail | Record API/user activity |
| Configuration/compliance | AWS Config | Track resource configuration and compliance |

**Exam clue:** Monitor → CloudWatch; API audit → CloudTrail; configuration/compliance → Config.

## **23\. Secure Secrets Management**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Application | Lambda/EC2/ECS | Requests secret at runtime |
| Secrets | AWS Secrets Manager | Store/rotate database credentials and secrets |
| Encryption | AWS KMS | Encrypt secret data with managed keys |

**Choose when:** Credentials/passwords/API secrets must not be hardcoded or stored in source code.

## **24\. Data Migration**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Source | On-premises database | Existing database |
| Migration | AWS Database Migration Service (DMS) | Migrate/replicate database data |
| Target | Amazon RDS/Aurora | Managed AWS database |

**Choose when:** Moving databases to AWS with minimal downtime; use ongoing replication for migration cutover.

## **25\. Cost-Optimized Batch Processing**

| Layer | Service(s) | Why |
| ----- | ----- | ----- |
| Compute | Amazon EC2 Spot Instances | Low-cost interruptible capacity |
| Scaling | EC2 Auto Scaling | Add/remove workers |
| Storage | Amazon S3 | Durable input/output |
| Optional orchestration | AWS Batch | Managed batch job scheduling |

**Choose when:** Workloads are flexible, interruptible, and can retry; avoid Spot for interruption-sensitive workloads.

## **26\. Disaster Recovery Patterns — RTO/RPO Decision**

| Strategy | RTO | RPO | Cost | How It Works |
| ----- | ----- | ----- | ----- | ----- |
| Backup & Restore | Hours | Hours | Lowest | Restore infrastructure/data after failure |
| Pilot Light | Tens of minutes | Minutes | Low | Core components/data replication remain ready; scale application tier |
| Warm Standby | Minutes | Seconds–minutes | Medium | Scaled-down production environment runs continuously |
| Multi-Site Active/Active | Near-zero | Near-zero | Highest | Full environments actively serve traffic in multiple Regions |

| 🧠 MNEMONIC | BR → PL → WS → AA |
| ----- | ----- |
| Exam rule | Lower RTO/RPO generally requires higher cost and readiness |

## **27\. Data Lake Architecture Pattern**

| Layer | Service(s) | Purpose |
| ----- | ----- | ----- |
| Ingestion | Kinesis, Data Firehose, AWS DMS, DataSync, Snowball Edge | Collect streaming, database, file, or on-premises data |
| Raw storage | Amazon S3 | Durable raw-data landing zone |
| Catalog | AWS Glue Data Catalog | Schemas, tables, partitions, metadata |
| Transform | AWS Glue / Lambda | Clean, transform, enrich |
| Curated storage | Amazon S3 | Analytics-ready data, e.g. Parquet/ORC |
| Query | Amazon Athena | Serverless SQL over S3 |
| Visualization | Amazon QuickSight | BI dashboards |
| Warehouse | Amazon Redshift | High-performance analytics/OLAP |

## **28\. Event-Driven Workflow / Orchestration**

| Scenario | Pattern | Service |
| ----- | ----- | ----- |
| Multi-step workflow with retries | State machine | AWS Step Functions |
| Long-running business process | Orchestration | Step Functions |
| Error handling/branching | Workflow logic | Step Functions |
| Event-triggered workflow | Event → state machine | EventBridge → Step Functions |

## **29\. Well-Architected Framework — 6 Pillars**

| Pillar | Core Question | Key Services/Practices |
| ----- | ----- | ----- |
| Operational Excellence | Run, monitor, improve? | CloudFormation, CloudWatch, Config, Systems Manager |
| Security | Protect systems/data? | IAM, KMS, CloudTrail, GuardDuty, WAF, Security Hub |
| Reliability | Recover and meet demand? | Multi-AZ, Multi-Region, Auto Scaling, backups, Route 53 |
| Performance Efficiency | Use resources efficiently? | Auto Scaling, Lambda, CloudFront, ElastiCache, Graviton |
| Cost Optimization | Avoid unnecessary cost? | Savings Plans, Spot, S3 lifecycle, right-sizing |
| Sustainability | Reduce environmental impact? | Right-sizing, managed/serverless services, efficient hardware |

| 💡 EXAM TIP | Well-Architected pillars |
| ----- | ----- |
| Memorize | Operational Excellence → Security → Reliability → Performance Efficiency → Cost Optimization → Sustainability |
| Decision rule | Identify the business requirement first; then choose the architecture/service that best satisfies it |

---

### **SAA-C03 Architecture Pattern Quick Decision Rules**

| Requirement / Exam Clue | Choose |
| ----- | ----- |
| Static website, low cost, global | S3 + CloudFront + Route 53 |
| Highly available web app | ALB + Auto Scaling + Multi-AZ database |
| No servers to manage | Lambda / Fargate / managed services |
| Decouple producer and consumer | SQS |
| One event to many consumers | SNS + SQS |
| React to AWS/service events | EventBridge |
| Orchestrate multi-step workflow | Step Functions |
| Private EC2 needs internet egress | NAT Gateway |
| Private subnet → S3/DynamoDB | Gateway VPC endpoint |
| Many VPCs need centralized routing | Transit Gateway |
| Shared Linux files | EFS |
| Windows SMB shares | FSx for Windows File Server |
| Reduce database reads/latency | ElastiCache |
| Relational + read-heavy | Aurora + read replicas |
| Database migration | DMS |
| Long-term archive | S3 Glacier storage classes |
| Regional disaster recovery | Route 53 + cross-Region replication |
| API usage plans/API keys or built-in WAF | API Gateway REST API |
| Simple lower-cost API | API Gateway HTTP API |
| Batch + interruption-tolerant | Spot Instances / AWS Batch |

---
