# AWS Storage Gateway

Bridges on-prem IT environments with AWS Cloud storage. Crucial for hybrid storage, cloud-native backups, DR, and offline migrations.

---

### 1. Deployment Models
Supported deployment platforms for Storage Gateway appliances.

| Platform | Type | Use Case / Note |
| :--- | :--- | :--- |
| **On-Prem VM** | VMware ESXi, MS Hyper-V, Linux KVM | Standard deployment; runs on virtualization host in local datacenter. |
| **AWS Cloud** | Amazon EC2 | Deployed as EC2 instance for DR, cloud mirroring, or EC2 app storage. |
| **Hardware** | Physical Appliance | Dedicated physical hardware (End of Availability: May 2025; Support ends: May 2028). |

---

### 2. Core Gateway Types Comparison

| Gateway Type | Local Protocol | AWS Storage Backend | Local Caching / Storage Strategy | SAA-C03 Exam Triggers & Target Scenarios |
| :--- | :--- | :--- | :--- | :--- |
| **S3 File Gateway** | NFS (v3, v4.1)<br>SMB (v2, v3) | Amazon S3<br>(S3 Standard, S3 Intelligent-Tiering, S3 Standard-IA, S3 One Zone-IA) | Local cache stores hot (recently accessed) data; 1:1 mapping of files to S3 objects. | • Low-latency NFS/SMB access to S3 objects.<br>• Flat file shares, database backups, or group file shares.<br>• Integrates with on-prem Active Directory (AD) for permissions. |
| **FSx File Gateway** | SMB (v1-v3) | Amazon FSx for Windows File Server | Caches frequently accessed Windows files on-prem. | • High-performance Windows file shares with AD integration.<br>• Windows Access Control Lists (ACLs) enforced natively.<br>• Local caching of files hosted in AWS in-cloud Windows FSx. |
| **Volume Gateway (Cached)** | iSCSI (Block) | Amazon S3 (Primary storage)<br>as iSCSI LUNs | Frequently accessed data cached locally. Rest of dataset is stored in S3. | • Low-cost, scalable block storage backed by S3.<br>• Low local physical storage capacity on-prem.<br>• Applications require iSCSI block interface (e.g. databases, VM disks). |
| **Volume Gateway (Stored)** | iSCSI (Block) | EBS Snapshots (backed by S3)<br>for backup | *Entire* dataset is stored on-prem on local physical disk. | • App requires absolute lowest possible latency R/W access to full dataset.<br>• On-prem has plenty of local storage capacity.<br>• Async, point-in-time backups to S3 as EBS snapshots. |
| **Tape Gateway (VTL)** | iSCSI VTL | Amazon S3 (Virtual tapes)<br>S3 Glacier (Archived tapes) | Caches virtual tapes locally for write buffer and fast reads. | • Replace physical tape library backup (Veeam, Commvault) with cloud-backed Virtual Tape Library.<br>• Retains legacy backup processes/software.<br>• Archive to Glacier Flexible Retrieval (3-5 hr retrieve) or Glacier Deep Archive (12 hr retrieve). |

---

### 3. Service Selector: Storage Gateway vs. DataSync vs. Snow Family
Key architectural differences commonly tested on SAA-C03.

| Feature | AWS Storage Gateway | AWS DataSync | AWS Snow Family |
| :--- | :--- | :--- | :--- |
| **Use Case** | **Continuous, real-time** hybrid storage & caching. | **Fast, automated network migration** / batch transfer. | **Offline physical shipping** of massive local datasets. |
| **Sync Type** | Real-time, continuous local cache syncing. | Scheduled, batch-based incremental synchronization. | Offline, one-time bulk data import/export. |
| **Protocols** | NFS, SMB, iSCSI, VTL | NFS, SMB, S3 API, HDFS | NFS, SMB, iSCSI, S3 API (on device) |
| **Exam Triggers** | "Mount S3 locally via NFS/SMB", "iSCSI LUN backed by S3", "VTL to replace tape". | "Automated daily copy of file shares to S3", "Fast WAN migration of 10 TB over active network". | "100 TB local database to move to S3", "limited on-prem internet bandwidth", "completely offline". |

---

### 4. SAA-C03 High-Yield Core Facts & Exam Traps

| Concept | AWS Standard Fact | SAA-C03 Trap / Direct Solution |
| :--- | :--- | :--- |
| **File vs. Volume** | File Gateways operate at **File-level** (NFS/SMB). Volume Gateways operate at **Block-level** (iSCSI). | • If client needs raw block devices or mounting a drive → **Volume Gateway**.<br>• If client needs file directories or standard shares → **File Gateway**. |
| **Tape Archiving** | Tapes are created in S3, then archived to S3 Glacier storage classes. | • **Glacier Flexible Retrieval**: 3-5 hours retrieval time.<br>• **Glacier Deep Archive**: 12 hours retrieval time.<br>• Archived tapes are read-only and must be retrieved to Tape Gateway first before reading. |
| **Local VM Disks** | Gateway VMs require two physical local disk allocations: Upload Buffer and Cache Storage. | • **Upload Buffer**: Holds writes locally before async upload to AWS.<br>• **Cache Storage**: Holds hot read data locally for sub-millisecond access. |
| **EBS Snapshot Restore** | Volume Gateway backups are stored as point-in-time EBS Snapshots. | • Can restore as EBS Volume to EC2 in AWS for DR.<br>• Can restore back to on-prem Volume Gateway as a new volume. |
| **DR & HA** | Storage Gateway supports VMware High Availability (VMware HA). | • For critical hybrid workloads requiring on-prem HA, configure VMware HA cluster. |
| **Encryption & Security** | Data encrypted in transit (SSL/TLS) and at rest (AES-256). | • Rest encryption uses SSE-S3 (default) or AWS KMS customer-managed keys (CMKs). |

---

### 🧠 Mnemonics & Instant Decision Shortcuts

| If the exam scenario asks for... | ...the correct AWS solution is: |
| :--- | :--- |
| **"Replace legacy physical tape backups"** | **Tape Gateway (VTL)** |
| **"Local AD-integrated SMB file share with lowest latency on Windows files"** | **FSx File Gateway** |
| **"NFS/SMB share to store database backups or flat files directly in S3"** | **S3 File Gateway** |
| **"iSCSI drive backed by S3 with low on-prem capacity"** | **Volume Gateway (Cached)** |
| **"iSCSI drive, entire dataset local, async cloud backup"** | **Volume Gateway (Stored)** |
| **"Automate nightly sync of on-prem file server to S3 without client mount"** | **AWS DataSync** |
| **"Migrate 80 TB database offline to AWS with no local internet bandwidth"** | **AWS Snowball Edge Storage Optimized** |
