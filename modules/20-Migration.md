### **Migration Services**

| Service | Migrates | Key Facts |
| :--- | :--- | :--- |
| AWS DMS (Database Migration Service) | Databases (homogeneous or heterogeneous) | • **Minimal downtime** during migrations.<br>• Supports **continuous replication** via CDC (Change Data Capture).<br>• Requires **SCT (Schema Conversion Tool)** for heterogeneous migrations (e.g., Oracle → PostgreSQL). |
| AWS SMS (Server Migration Service) | On-prem virtual machines | • **Lift-and-shift** VMs to EC2.<br>• Performs **incremental replication** of VM volumes.<br>• **Agentless** architecture.<br>• **Superseded** by Application Migration Service (MGN). |
| AWS Application Migration Service (MGN) | Physical, virtual, cloud servers | • Recommended primary service for **lift-and-shift** migrations.<br>• Uses **continuous, block-level replication**.<br>• Enables cutovers with **minimal downtime**.<br>• **Replaces** AWS SMS. |
| AWS Snowball Edge (Storage Optimized) | Large data volumes (TBs to PBs) offline | • Provides **80 TB** of usable storage.<br>• Ideal for **petabyte-scale** offline transfers.<br>• For environments with **limited network bandwidth**. |
| AWS Snowmobile | Exabyte-scale data transfer | • A physical, **45-foot shipping container** truck.<br>• Cost-effective for data transfers **exceeding 10 PB**. |
| AWS DataSync | File/NFS/SMB data to AWS (online) | • **Automates/accelerates** online data sync.<br>• Syncs **NFS, SMB, or HDFS** with S3, EFS, or FSx.<br>• **Agent-based** software.<br>• **Faster** than standard transfer tools (like cp) over DX. |
| AWS Transfer Family | SFTP/FTP/FTPS to/from S3 or EFS | • **Fully managed** secure file transfer server.<br>• Direct integration with **Amazon S3 or EFS**.<br>• Requires **no client or protocol changes**. |

| 💡 **TIP:** Snowball decision: < 10 PB → Snowball Edge (multiple units). > 10 PB → Snowmobile. Rule of thumb: if network transfer takes > 1 week, use Snowball. |
| :--- |