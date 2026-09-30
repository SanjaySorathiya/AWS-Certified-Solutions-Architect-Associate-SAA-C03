###  **AWS Containers - ECS, EKS, Fargate**

Containers abstract runtime envs. SAA-C03 tests identifying the proper container platform for each architecture scenario.

| Service | Description | Launch Types | Key SAA-C03 Exam Scenario |
| :--- | :--- | :--- | :--- |
| **ECS** (Elastic Container Service) | AWS-native container orchestration; simpler, tightly integrated with AWS services | EC2 (user-managed) or Fargate (serverless) | "AWS-native", "simpler alternative to K8s", "Docker on AWS", "native ALB/NLB routing" |
| **EKS** (Elastic Kubernetes Service) | Managed K8s; industry standard; highly portable across clouds & On-prem | EC2, Fargate, or AWS Outposts | "K8s-native", "multi-cloud migration", "team has K8s expertise", "complex microservices" |
| **Fargate** | Serverless compute engine for ECS/EKS tasks; removes server overhead | N/A — it IS a launch type | "Zero EC2 management", "no host patching/scaling", "pay-per-vCPU & RAM consumption" |
| **App Runner** | Fully managed service; builds & deploys container or source code directly | Fully managed | "Simplest deploy", "just run web app/API", "no VPC infrastructure or load balancers to configure" |
| **ECR** (Elastic Container Registry) | Secure, managed Docker container image registry | N/A — storage only | "Store private/public Docker images", "native IAM authentication for ECS/EKS image pulls" |

---

### **Key ECS & ECR Architectural Concepts**

| Concept | Detail / Mechanism | SAA-C03 Exam Integration |
| :--- | :--- | :--- |
| **ECS Task Role** | IAM role assigned directly to individual ECS tasks | Grants container apps specific permissions to access AWS resources (S3, DynamoDB, SQS) |
| **ECS Task Execution Role** | IAM role used by ECS Agent & Fargate infrastructure | Grants permissions to pull images from ECR, push container logs to CW Logs, & fetch Secrets Mgr values |
| **AWS Copilot** | Developer-focused CLI tool | Simplifies container app building, releasing, & operating on ECS/Fargate |
| **ECR Image Scanning** | Automatic vulnerability scanning for container images | Scans on push or on-demand to detect OS CVEs; triggers SNS notifications on critical findings |
| **ECR Lifecycle Policies** | Automated cleanup of unused or old image tags | Deletes untagged/expired images to lower storage costs across accounts |
| **EKS Pod Networking** | VPC CNI (Container Network Interface) plugin | Assigns native VPC IP addresses directly to K8s Pods for optimal HA networking performance |

---

| ⚠️ EXAM TRAP: Fargate is NOT a standalone service — it is a launch type for ECS/EKS. SAA-C03 'serverless containers' scenarios require ECS/EKS with Fargate, NOT a separate Fargate service. |
| :--- |
