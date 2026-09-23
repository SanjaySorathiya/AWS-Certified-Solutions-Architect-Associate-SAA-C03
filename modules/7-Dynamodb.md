### **DynamoDB — Serverless NoSQL DB**

DynamoDB is deeply integrated across AWS. Master consistency models, capacity modes, indexing, & streams.

#### **DynamoDB Core Concepts**

| Concept | Detail | Exam Implication |
| :--- | :--- | :--- |
| **Primary Key** | Simple (PK only) or Composite (PK + SK). | Design PK for uniform distribution; avoid hot partitions. |
| **Read Consistency** | **Eventual Consistent** (default, 0.5 RCU/4KB)<br>**Strong Consistent** (1 RCU/4KB)<br>**Tx Consistent** (2 RCU/4KB, ACID) | Financial Apps needing latest data -> Strong/Tx Consistent; extra cost (2x RCU). |
| **Capacity Modes** | **Provisioned**: Specify RCU/WCU; auto-scaling supported; predictable cost.<br>**On-Demand**: Pay-per-request; auto-scaling instantaneous; variable load. | On-Demand for unpredictable spikes; Provisioned + Auto Scaling for steady workloads. |
| **Streams** | **DynamoDB Streams**: Ordered item changes; 24-hr retention; triggers Lambda.<br>**Kinesis Streams**: Up to 1-yr retention; advanced analytics. | Event-driven: change data capture (CDC), cross-Reg replication, triggers. |
| **TTL** | Auto-delete items based on Epoch timestamp (seconds); best-effort deletion within 48 hrs. | Expire sessions, logs, & ephemeral data at zero WCU cost. |
| **DAX** | Write-through in-memory cache; microsecond read latency; query & item caching. | Read caching with zero App code changes; bypasses DB hot partitions. |
| **Global Tables** | Multi-Reg, multi-active replication; requires DynamoDB Streams (New & Old Images). | Active-active replication: local reads/writes in multiple Regs. |
| **Transactions (Tx)** | ACID operations across multiple tables/items. Max 100 items or 4MB. | Tx operations consume 2x RCU (2 RCU/4KB) & 2x WCU (2 WCU/1KB). |

#### **DynamoDB Indexes**

| Index Type | Created When | Key Structure | Read Consistency | Throughput | Use Case |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **LSI** | Table creation **ONLY** | Same PK, different SK | Strong or Eventual | Shares parent table's RCU/WCU | Query same PK with different sort order |
| **GSI** | Any time (create/delete) | Different PK &/or SK | Eventual **ONLY** | Dedicated GSI RCU/WCU | Query on any attribute; most flexible |

| 🧠 **MNEMONIC**: **LSI** = Local = Same PK = Launch only. **GSI** = Global = Greater flexibility = can add anytime, own capacity. |
| :--- |