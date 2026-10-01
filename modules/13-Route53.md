### **Route 53 — DNS & Routing Policies**
AWS’s highly available and scalable DNS web service. Essential for SAA-C03 to know when and how to route traffic, resolve domains, and handle failover.

#### **Public vs. Private Hosted Zones**
| Type | Scope | Key Features | SAA Scenario Keyword |
| :--- | :--- | :--- | :--- |
| **Public** | Internet | Routes internet traffic to AWS resources or external IPs. | "Public website routing", "Internet-facing resources" |
| **Private** | Internal | Routes traffic within one or more VPCs. Requires VPC settings: `enableDnsHostnames` and `enableDnsSupport` set to `true`. | "Internal DNS resolution", "Private VPC domain names" |

#### **Record Types: CNAME vs. Alias**
| Feature | CNAME Record | Alias Record |
| :--- | :--- | :--- |
| **Target** | Points hostname to another hostname (e.g., `test.com` -> `lb.aws.com`). | Points hostname to AWS resource (e.g., ALB, NLB, S3, CloudFront). |
| **Zone Apex** | ❌ **No**. Cannot be created for root domain (e.g., `example.com`). |  **Yes**. Works for both root (apex) and subdomains. |
| **Cost & Speed** | Queries billed by AWS. Client must do extra DNS lookup. | **Free** queries. Resolved internally by Route 53 (faster). |
| **TTL** | Required. | Automatically managed (dynamic update on AWS resource changes). |

#### **Route 53 Routing Policies**
| Policy | Mechanism | Use Case | SAA Scenario Keyword |
| :--- | :--- | :--- | :--- |
| **Simple** | Maps domain to single/multiple resources. Returns all IPs; client picks randomly. | Single server, stateless backends. | "basic DNS", "single endpoint", "round-robin" |
| **Weighted** | Splits traffic via weights (0–255). Weight `0` stops traffic. | A/B testing, blue/green deployment, canary release. | "split 10% to new version", "gradual migration" |
| **Latency-based** | Routes users to AWS Region with lowest network latency. | Global apps with strict latency requirements. | "lowest latency", "fastest response globally" |
| **Failover** | Active-Passive. Routes to secondary endpoint when primary health check fails. | Disaster Recovery (DR), active-standby. | "failover", "DR", "active-passive standby" |
| **Geolocation** | Routes traffic based on user's physical location (continent, country, state). | Content localization, GDPR/data compliance, language lock. | "Europe users to EU servers", "data residency" |
| **Geoproximity** | Routes based on physical location of users and resources. Uses **Bias** (-99 to 99) to shift boundaries. | Dynamic traffic engineering, fine-grained geographic shifts. | "expand/shrink region footprint", "shift traffic with Bias" |
| **Multi-Value** | Returns up to 8 healthy records with simple health checks. | Client-side load balancing. *Note: NOT an ELB replacement.* | "client-side LB", "up to 8 healthy endpoints" |
| **IP-Based** | Routes traffic based on client subnet (user's CIDR blocks). | Specific ISP routing, optimize network costs. | "route specific CIDR ranges", "ISP-based routing" |

| ⚠️ EXAM TRAP: Geolocation vs. Geoproximity: Geolocation is STRICT (e.g., France users go to EU endpoint). Geoproximity uses location + BIAS to dynamically expand/shrink regional coverage for TRAFFIC ENGINEERING. To use geoproximity, you must use Route 53 Traffic Flow. |
| :--- |

#### **Route 53 Health Checks**
| Type | Mechanism | Configuration / SAA Tips |
| :--- | :--- | :--- |
| **Endpoint** | Monitors HTTP, HTTPS, or TCP. 15 global checkers verify target. | Threshold: default 3 tries (pass/fail). Must configure SGs to allow Route 53 IP ranges. |
| **Calculated** | Combines up to 256 child health checks using AND, OR, or NOT logic. | Parents pass/fail based on defined health of child endpoints. |
| **CW Alarm** | Monitors CloudWatch metrics/alarms. | Essential for private resources inside VPCs (no internet access). |

#### **Hybrid Cloud DNS: Route 53 Resolver**
| Endpoint Type | Function | Traffic Flow |
| :--- | :--- | :--- |
| **Inbound** | Resolves AWS internal resources (VPC) from on-premises. | On-premises DNS -> Resolver Inbound IP -> AWS VPC |
| **Outbound** | Resolves on-premises resources from AWS VPC. | AWS VPC -> Route 53 Resolver Rule -> Outbound IP -> On-prem DNS |

---
