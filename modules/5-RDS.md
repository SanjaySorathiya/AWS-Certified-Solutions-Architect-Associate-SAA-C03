### **RDS — Relational Database Service**

#### **RDS Deployments — Single-AZ vs Multi-AZ vs Read Replicas (RRs)**

| Feature | Single-AZ | Multi-AZ DB Instance (Passive) | Multi-AZ DB Cluster (Active) | Read Replica (RR) |
| :--- | :--- | :--- | :--- | :--- |
| **Primary Purpose** | Dev/Test (Low Cost) | High Availability (HA) & Failover | HA + Read Scaling + Low Latency | Read Scaling & Performance |
| **Replication** | N/A | Synchronous (Standby in another AZ) | Synchronous (2 Standbys in 2 other AZs) | Asynchronous (Slight lag possible) |
| **Read Capacity** | Primary DB only | Primary DB only (Standby passive/non-readable) | Primary + 2 Standbys are all readable | RRs handle read-heavy traffic |
| **Failover Action** | Manual restore from backup | Automatic DNS flip (~1-2 mins) | Automatic DNS flip (<35 secs, ultra-fast) | Manual promotion to primary DB |
| **Region Scope** | 1 AZ in 1 Region | Multi-AZ within same Region | Multi-AZ within same Region | Can be Cross-Region (helps with DR) |
| **Data Cost** | N/A | Free replication data transfer | Free replication data transfer | Cross-Region RRs incur transfer cost |
| **Key Exam Cues** | "non-production", "cost-saving" | "HA", "failover", "DR within same Region" | "HA + read scaling", "under 35s failover" | "read scaling", "reporting", "analytics" |

| ⚠️ EXAM TRAP: Multi-AZ DB Instance does NOT scale read performance since standby is passive. For HA AND read scaling, use Multi-AZ DB Cluster, or combine Multi-AZ DB Instance with RRs. |
| :--- |

#### **RDS Backup (Bkp) & Restore**

| Backup Type | Trigger & Retention | Restore Behavior & Impact | Storage & Portability |
| :--- | :--- | :--- | :--- |
| **Auto Bkp** | • Daily snaps + txn logs.<br>• Retained 1-35 days.<br>• Deleted on DB termination unless specified. | • Always restores to **NEW** DB instance & endpoint (cannot overwrite in-place).<br>• Point-in-Time Restore (PITR) down to second.<br>• Single-AZ: Brief I/O pause on primary.<br>• Multi-AZ: Bkp taken from standby (No primary I/O impact). | • Stored in S3.<br>• Cannot share directly; must copy to Manual Snap to share. |
| **Manual Snap** | • User-initiated.<br>• Retained indefinitely until manually deleted. | • Restores to **NEW** DB instance & endpoint.<br>• No performance impact during snapshot creation on Multi-AZ. | • Stored in S3.<br>• Can share across AWS accounts & copy across Regions. |

#### **RDS Security, Authentication & Encryption**

| Security Domain | Strategy & AWS Best Practices | SAA-C03 Exam Critical Details |
| :--- | :--- | :--- |
| **Encryption at Rest** | • AWS KMS (AES-256) for DB storage, backups, read replicas, and logs. | • **Must enable at creation**.<br>• Cannot encrypt existing unencrypted DB directly.<br>• **Workaround**: Snapshot DB → Copy snapshot with KMS Encryption enabled → Restore new encrypted DB from copy. |
| **Encryption in Transit** | • SSL/TLS certificates. | • Force SSL by setting parameter `rds.force_ssl=1` in DB Parameter Group. |
| **Network Security** | • Private Subnets + SGs. | • Deploy DBs only in private subnets.<br>• SG should only allow inbound traffic from application tier SG on DB port (e.g., TCP 3306 for MySQL). |
| **IAM DB Auth** | • Use IAM roles & short-lived (15-min) tokens instead of passwords. | • Best for serverless (Lambda) or EC2 instances.<br>• Eliminates hardcoded DB credentials.<br>• Network traffic still encrypted via SSL/TLS. |

#### **RDS Advanced Features: Proxy, Autoscaling & Custom**

| Feature | Primary Use Case & How It Works | SAA-C03 Exam High-Yield Benefits |
| :--- | :--- | :--- |
| **RDS Proxy** | • Fully managed, highly available database proxy.<br>• Pools & shares established DB connections. | • Solves Lambda connection exhaustion issues.<br>• Reduces Multi-AZ failover time by up to 66%.<br>• Enforces IAM DB Auth; integrates with AWS Secrets Manager. |
| **Storage Autoscaling** | • Auto-increases DB storage when free space < 10% for >= 5 mins. | • Avoids manual provisioning & storage outages.<br>• Zero downtime or impact on active workloads. |
| **RDS Custom** | • Managed DB service for Oracle & SQL Server.<br>• Grants full OS-level (SSH/RDP) & DB engine admin access. | • Best when third-party software/agents require local installation or specific OS configurations. |

--------------------------------------------------------------------------------
