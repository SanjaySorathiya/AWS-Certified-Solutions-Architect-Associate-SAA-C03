### **Virtual Private Cloud - VPC**

VPC is your private network in AWS. The SAA-C03 exam tests VPC design constantly — subnets, routing, security, and connectivity patterns.

#### **1. VPC Fundamentals**

| Component | Description | Key Facts |
| :--- | :--- | :--- |
| **VPC** | Logically isolated network; spans all **AZs** in a region. | Max 5 VPCs per region (soft limit); CIDR range `/16` to `/28`. Primary CIDR cannot be changed; secondary CIDRs can be added. |
| **Subnet** | **AZ**-specific subdivision of a VPC CIDR block. | Public (route to **IGW**) vs Private (no **IGW** route). AWS reserves **5 IPs** per subnet (`.0` Network, `.1` VPC Router, `.2` Amazon DNS/Route 53 Resolver, `.3` future use, `.255` Broadcast). |
| **Internet Gateway (IGW)** | Enables internet access for public subnets; horizontally scaled, **HA**, no bandwidth limit. | One **IGW** per VPC; attach to VPC, add route `0.0.0.0/0` -> `igw-xxxx` in public subnet **RT**. Instances require public IPv4/EIP. |
| **NAT Gateway** | Allows PRIVATE subnet instances to initiate outbound IPv4 connections; blocks inbound. | Deployed IN public subnet; uses Elastic IP (**EIP**). Regional **HA** option (not **AZ**-resilient). For true **HA**, deploy one **NAT Gateway** per **AZ** and route to the local **NAT Gateway**. |
| **Route Table (RT)** | Rules directing traffic based on destination CIDR. | Each subnet associates with one **RT**; subnets without explicit association use Main **RT**. Every **RT** has an immutable local route for intra-VPC traffic. |
| **Security Group (SG)** | Stateful virtual firewall at instance (**ENI**) level; only **ALLOW** rules. | Stateful = return traffic auto-allowed. Default: all outbound allowed, all inbound denied. Rules can reference other **SGs** by ID (not just CIDRs). |
| **Network ACL (NACL)** | Stateless firewall at subnet level; **ALLOW** and **DENY** rules. | Stateless = must explicitly allow return traffic (ephemeral ports 1024-65535). Evaluated in order by rule number (lowest first). |
| **VPC Peering** | Direct private connection between two VPCs (same or different account/region). | **NOT transitive** — A↔B and B↔C does NOT mean A↔C. No overlapping CIDRs allowed. Edge-to-edge routing not supported. |
| **VPC Endpoints (VPCE)** | Private connection to AWS services without internet (**IGW**, NAT Gateway, **VPN**, or **DX**). | **Gateway** (S3, DynamoDB — free; configure via **RT** routes) vs **Interface (PrivateLink)** (most services — uses **ENIs** with private IPs; hourly cost + data transfer). |

| ⚠️ EXAM TRAP: NAT Gateway is NOT highly available (HA) across AZs by default. For true HA, deploy one NAT Gateway per AZ and configure each private subnet to route to the NAT Gateway in ITS OWN AZ. |
| ------ |

| 💡 IPV6 TIP: NAT Gateways are for IPv4. For IPv6 in private subnets, use an Egress-Only Internet Gateway (Egress-Only IGW). It allows outbound IPv6 traffic but blocks all inbound IPv6 connections. No EIP required. |
| ------ |

| 💡 SSM TIP: Replace public Bastion hosts with SSM Session Manager. No Bastions, no public subnets, no IGW, and no inbound ports (22/3389) need to be open in SGs. Communication goes through SSM VPC endpoints. |
| ------ |

#### **2. Security Group vs NACL — The Classic Comparison**

| Property | Security Group (SG) | Network ACL (NACL) |
| :--- | :--- | :--- |
| **Level** | **Instance (ENI)** | **Subnet** |
| **State** | **STATEFUL** — return traffic auto-allowed | **STATELESS** — must allow both directions (including ephemeral ports 1024-65535) |
| **Rules** | **Allow only** (whitelist) | **Allow and Deny** |
| **Evaluation** | **All rules evaluated** together before deciding | **Rules evaluated in numerical order** (lowest first); first match wins |
| **Default behavior** | Deny all inbound; allow all outbound | **Default NACL**: Allows all traffic. **Custom NACL**: Denies all traffic. |
| **Target References** | Rules can reference **other SGs by ID** (e.g., allow DB SG from App SG) | Rules can only reference **CIDR blocks** (cannot reference SG/NACL IDs). |
| **Use case** | Primary security control per workload (tiered app security) | Additional subnet-level security, blocking specific malicious IP ranges (DDoS mitigation). |

| 💡 SG ID TIP: SGs can reference other SGs by ID (not CIDR). Highly tested for tiered security: allow inbound to DB SG from App SG without hardcoding IP addresses. |
| ------ |

#### **3. VPC Connectivity Options**

| Option | Use Case | Key Characteristics |
| :--- | :--- | :--- |
| **VPC Peering** | Connect 2 VPCs directly. | - Non-transitive; 1:1 relationship.<br>- Any account/region.<br>- No overlapping CIDR blocks allowed.<br>- Edge-to-edge routing not supported. |
| **Transit Gateway (TGW)** | Hub-and-spoke: connect many VPCs + on-prem. | - Transitive routing; 1 **TGW** can handle thousands of VPCs.<br>- Route tables on **TGW**; share via AWS RAM.<br>- Supports **IP Multicast**. |
| **AWS PrivateLink** | Expose service to other VPCs without peering. | - Service runs in **NLB**; consumers get Interface Endpoint (**ENI**).<br>- Uni-directional (consumer to provider).<br>- Does not expose full VPC CIDR. |
| **Site-to-Site VPN** | Encrypted IPsec tunnel over public internet. | - Fast to set up (~hours); variable latency.<br>- Up to **1.25 Gbps** per tunnel (can scale via ECMP over multiple tunnels).<br>- Uses **VGW**/**TGW** on AWS, and Customer Gateway (**CGW**) on-prem. |
| **Direct Connect (DX)** | Dedicated private line from on-prem to AWS. | - Consistent low latency; 1/10/100 Gbps port speeds.<br>- Bypasses public internet; **no encryption** by default.<br>- Setup takes weeks/months. |
| **Direct Connect + VPN** | Encrypted tunnel over Direct Connect. | - Best of both: dedicated bandwidth (**DX**) + encryption (**VPN** IPsec) for compliance/regulated workloads. |
| **VPN CloudHub** | Hub-and-spoke VPN for multiple on-prem sites. | - Uses **VGW**; sites can communicate with each other through AWS network. |

| 🧠 MNEMONIC: VPN = Variable/Virtual (goes over internet, encrypted, variable latency/speed). DX = Dedicated eXpress (physical line, consistent speed, no encryption). For both consistency and encryption: use DX + VPN. |
| ------ |

#### **4. VPC Flow Logs & Advanced Diagnostics**

| Aspect | Specifications & Key Facts |
| :--- | :--- |
| **What It Captures** | IP traffic metadata (flow of packets: src/dst IP, port, protocol, action [ACCEPT/REJECT]). Does NOT capture packet payloads. |
| **Exclusions (Not Captured)** | DNS traffic (unless using AWS DNS), Windows license activation, Instance Metadata (`169.254.169.254`), DHCP, KMS, and local routing. |
| **Destinations** | **S3** (cost-effective, long-term storage, Athena queryable), **CloudWatch Logs** (real-time monitoring, metric filters, alarms), **Kinesis Data Firehose** (stream to SIEM or third-party analysis). |
| **Scope Levels** | VPC, Subnet, or ENI level. |
| **Diagnostics Tools** | **VPC Reachability Analyzer** (static analysis of network paths based on configuration), **Route Analyzer** (analyzes Transit Gateway route tables). |

--------------------------------------------------------------------------------