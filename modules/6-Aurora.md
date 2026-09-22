### **Aurora**

| Detail | Core Overview |
| :--- | :--- |
| **Type** | AWS proprietary cloud-native relational DB (MySQL/PostgreSQL compatible). |
| **Design** | Decoupled compute & storage; purpose-built for cloud. |
| **Focus** | High-performance, high-availability SAA-C03 priority. |

#### **Aurora Architecture Highlights**

| Feature | Amazon Aurora | Standard RDS |
| :--- | :--- | :--- |
| **Storage** | • Shared cluster volume auto-grows up to 128 TiB<br>• 6-way replication (6 copies across 3 AZs)<br>• Self-healing, peer-to-peer storage replication | • EBS storage (gp2/gp3/io1/io2) up to 64 TiB<br>• Single or Multi-AZ synchronous replication<br>• Manual storage scaling (or auto-scaling) |
| **Read Replicas** | • Up to 15 Aurora Replicas<br>• Sub-millisecond replication lag<br>• Automatic scaling of replica fleet | • Up to 5 Read Replicas (some engines support 15)<br>• Asynchronous replication (higher lag)<br>• Manual replication scaling |
| **Failover** | • Under 30 seconds (usually under 10 seconds)<br>• Promotes lowest-tier replica to writer<br>• Zero data loss due to shared storage volume | • 1 to 2 minutes for Multi-AZ failover<br>• Swaps DNS CNAME to standby instance<br>• Standby does not accept read traffic |
| **Backtrack** | • Rewind DB to precise time in seconds without restore<br>• Configurable up to 72 hours (MySQL only) | • N/A (must perform PITR to new DB instance) |
| **Scaling** | • **Serverless v2**: Auto-scales compute (ACUs) in seconds<br>• Pay-per-use, scales down to 0.5 ACU | • Manual instance scaling (requires brief downtime) |
| **Global Reach** | • **Global Database**: 1 primary region + up to 5 secondary regions<br>• Physical replication lag < 1 second<br>• Secondary regions for low-latency local reads<br>• DR: RTO < 1 minute, RPO < 1 second | • Cross-Region Read Replicas (logical replication)<br>• Higher replication lag<br>• Manual promotion required for DR |

#### **Aurora Connection Endpoints**

| Endpoint Type | Purpose & Behavior |
| :--- | :--- |
| **Writer Endpoint** | Points to primary DB instance; handles all write operations. |
| **Reader Endpoint** | Load-balances read connections across all available Aurora Replicas. |
| **Custom Endpoint** | Groups user-defined subset of DB instances; separates analytics/operational workloads. |

#### **Advanced Exam Concepts**

| Concept | Architecture Details |
| :--- | :--- |
| **Quorum Design** | Writes require 4 of 6 nodes; reads require 3 of 6 nodes (highly resilient). |
| **Fast Cloning** | Instant database clones using copy-on-write; saves storage and time. |
| **Parallel Query** | Pushes query processing down to Aurora storage tier for faster analytics. |

#### **💡 SAA-C03 Exam Target Scenarios**

| Scenario Requirements | Recommended Solution |
| :--- | :--- |
| Multi-region DR with RTO < 1 min, RPO < 1 sec | **Aurora Global Database** with cross-region physical replication. |
| DB workload with unpredictable, highly variable traffic | **Aurora Serverless v2** scaling instantly based on application load. |
| Rapid rollback from user errors (e.g., accidental delete) | **Aurora Backtrack** to rewind DB without downtime or backups restore. |
| Separate BI/analytics traffic from production web app | **Custom Endpoints** to route BI queries to dedicated Read Replicas. |

--------------------------------------------------------------------------------