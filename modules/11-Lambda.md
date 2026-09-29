### **AWS Lambda**
Lambda runs code without provisioning servers. SAA-C03 tests limits, triggers, VPC integrations, and serverless vs server-based architectures.

#### **Lambda Key Facts: Constraints & Resource Limits**
| Metric | Limit / SAA-C03 Exam Detail |
| ------ | ------ |
| Max Execution Timeout | 15 mins (900s) |
| Memory Range | 128 MB to 10,240 MB (10 GB); CPU scales proportionally (1 vCPU at 1,769 MB) |
| Ephemeral Storage (/tmp) | 512 MB to 10,240 MB (10 GB) (configurable) |
| Deployment Package Limits | 50 MB (zipped zip), 250 MB (unzipped), 10 GB (container image) |
| Concurrent Executions | 1,000 per region (default soft limit, can request increase) |
| Reserved Concurrency | Restricts max concurrent instances for a function; guarantees capacity and prevents throttling others |
| Provisioned Concurrency | Pre-warms execution environments to eliminate cold start latency; incurs extra cost |
| Function URLs | Dedicated HTTP(S) endpoint with IAM or NONE auth and CORS support; bypasses ALB/API Gateway |
| Layers | Reuse common libraries; up to 5 layers per function; reduces deployment package size |
| Pricing Model | Per request ($0.20 per 1M requests) + per GB-second of compute time |

#### **Lambda vs EC2: Serverless Event-Driven vs Dedicated Server Architecture**
| SAA-C03 Scenario Parameter | Choose Lambda (Serverless) | Choose EC2 (Server-based) |
| ------ | ------ | ------ |
| Traffic Pattern | Event-driven, highly sporadic, or idle for long periods | Continuous, steady-state, or predictable baseline load |
| Max Task Duration | Tasks completed in under 15 mins | Long-running jobs or batch processes exceeding 15 mins |
| Management Overhead | Zero server admin; AWS handles OS patching and scaling | Full operating system (OS) control, custom kernels, or patching required |
| Cost Efficiency | Paid only during active execution (zero cost when idle) | Pay continuously for running instances (use Reserved Instances to cut costs) |
| State and Storage | Stateless architecture; ephemeral storage via local `/tmp` | Stateful applications requiring persistent storage via EBS |

#### **Lambda Integration & Invocation Types**
| Invocation Mode | Triggering AWS Services | Scaling & Retry Behavior | Destinations & Error Handlers |
| ------ | ------ | ------ | ------ |
| **Synchronous** | API Gateway, ALB, Cognito, Step Functions, CLI/SDK | Caller blocks and waits for function execution to complete. Caller is responsible for retries. | No destinations. Errors must be handled on the caller side. |
| **Asynchronous** | S3, SNS, EventBridge, CloudWatch Logs/Events, SES | Event queued; Lambda returns 202 Accepted immediately. Automatically retries twice on failure. | Supports on-success/on-failure destinations (SQS, SNS, EventBridge, Lambda). Modern DLQ replacement. |
| **Event Source Mapping** | SQS, DynamoDB Streams, Kinesis, MSK, MQ | Lambda polls source, fetches batch, and invokes function. Processes up to 10 messages per batch for SQS. | On SQS failure, message returns to queue (up to maxReceiveCount), then go to SQS DLQ. Use Partial Batch Response to report only failed messages. |

#### **Lambda Security, Network & VPC Access**
| Architectural Component | SAA-C03 Configuration Detail | Exam Relevance & Best Practices |
| ------ | ------ | ------ |
| **IAM Execution Role** | IAM role attached directly to the Lambda function. | Grants Lambda permissions to call other AWS services (e.g., S3, DynamoDB, CloudWatch Logs). |
| **Resource-Based Policy** | Policy attached to Lambda granting permission to external services. | Allows other services or AWS accounts to invoke the Lambda function (e.g., S3 triggering Lambda on object write). |
| **VPC Connection** | Configure Lambda with VPC, private subnets in multiple Availability Zones (AZs), and Security Groups (SGs). | Essential to access private database instances (e.g., RDS, ElastiCache, private ALBs). Uses Hyperplane ENI mapping for rapid scaling. |
| **Outbound Internet Access** | VPC-connected Lambda has no native internet route. | Must route outbound traffic through a NAT Gateway in a public subnet to reach public endpoints or external APIs. |

#### **Edge Compute Options: CloudFront (CF) Functions vs Lambda@Edge**
| Architectural Metric | CloudFront (CF) Functions | Lambda@Edge |
| ------ | ------ | ------ |
| **Execution Point** | Edge Locations (225+ global sites close to users) | Regional Edge Caches (13+ regional locations) |
| **Max Execution Time** | 1 millisecond (extremely fast) | 5 seconds (viewer requests) / 30 seconds (origin requests) |
| **Runtime Language** | JavaScript (lightweight JS engine) | Node.js or Python (full runtime) |
| **Deployment Package Size** | Max 10 KB | Max 1 MB (viewer) / 50 MB (origin) |
| **Network & File Access** | No network or file system access | Full outbound network, file system, and AWS SDK access |
| **Best SAA-C03 Use Case** | High-scale, low-latency header modification, URL redirects, viewer request/response manipulation | Complex tasks, database queries, external API calls, body transformations, customizable origin request/response |

--------------------------------------------------------------------------------