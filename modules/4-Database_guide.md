### **Database Selection Guide (SAA-C03)**

Choosing the optimal DB engine is a core SAA-C03 skill. The AWS exam presents business or technical workloads and expects you to select the correct DB engine.

---

#### **Core DB Engine Reference**

| DB Engine | Type | Best For | Key SAA-C03 Differentiators |
| :--- | :--- | :--- | :--- |
| **RDS** | Relational (SQL) | Traditional OLTP, existing SQL apps, structured data | Managed SQL (MySQL, Postgres, MariaDB, Oracle, SQL Server). Multi-AZ (Sync replication for HA/failover). RRs (Async replication for scaling reads; can be cross-region). PITR (1-35 days). Storage: gp2/gp3, io1/io2. |
| **Aurora** | Relational (MySQL/Postgres) | Enterprise-grade OLTP, high-perf auto-scaling SaaS | Proprietary. 5x faster than MySQL, 3x faster than Postgres. Auto-scaling storage (up to 128 TiB). 6-copy storage replicated across 3 AZs. Up to 15 RRs with low replication lag. Aurora Serverless v2. Aurora Global DB for cross-region DR with <1 sec RPO. Backtrack (rewind DB without restore). |
| **DynamoDB** | NoSQL (Key-Value / Document) | Ultra-scale apps, mobile backends, gaming, IoT, session management | Serverless, single-digit ms latency at any scale. On-Demand or Provisioned WCU/RCU (with auto-scaling). Global Tables (Active-Active multi-region replication). DynamoDB Streams (real-time item changes; triggers Lambda/Kinesis). PITR. |
| **MemoryDB for Redis** | Durable In-Memory NoSQL | Ultra-fast apps needing extreme read/write performance + durability | Redis-compatible. Durable primary DB (unlike ElastiCache). Multi-AZ transactional log replication before acknowledging writes. Microsecond read, low single-digit ms write. HA & security. |
| **ElastiCache (Redis)** | In-Memory Key-Value Cache | Session state, caching, real-time leaderboards, pub/sub, geospatial | Sub-ms latency. Supports complex data types. Multi-AZ auto-failover, RRs, backup/restore, append-only files (AOF). Primarily used to offload relational DB reads. |
| **ElastiCache (Memcached)** | In-Memory Cache (Simple) | Simple distributed cache to offload DB reads, stateless apps | No data persistence. No replication (horizontal scaling via sharding only). Multi-threaded architecture. Best for simple, volatile caching. |
| **Redshift** | Columnar Data Warehouse (SQL) | OLAP, BI, complex analytics on petabyte-scale datasets | Columnar storage, MPP (Massively Parallel Processing). Redshift Spectrum (query S3 cold data directly via external tables). Redshift Serverless. Concurrency Scaling (auto-adds capacity for spike queries). |
| **Neptune** | Graph | Social networks, fraud detection, recommendation engines, identity graphs | Managed graph DB. Gremlin, SPARQL, openCypher. 3-AZ replication (6 copies). HA & DR. Highly connected datasets. |
| **DocumentDB** | Document (MongoDB-compatible) | Content management systems, catalogs, user profiles | MongoDB API compatible. Decouples compute & storage. Auto-scales storage. 3-AZ replication (6 copies). Managed, highly scalable. |
| **Keyspaces** | Wide Column (Cassandra-compatible) | High-write IoT, device telemetry, time-series, Cassandra migration | Cassandra Query Language (CQL) compatible. Serverless, pay-per-request. Single-digit ms performance. |
| **Timestream** | Time Series | IoT telemetry, application metrics, clickstream, DevOps logging | High-speed ingestion. Auto-tiering storage: memory (fast ingest/reads) & magnetic (low-cost retention). Built-in analytics (smoothing, interpolation, approximation). |
| **QLDB** | Ledger (Immutable) | Financial transactions, audit trails, regulatory compliance, supply chain | Cryptographically verifiable transaction log. Append-only, immutable history. Centrally managed by single trusted owner (not blockchain). |
| **OpenSearch Service** | Search & Analytics | Log analytics, full-text search, dashboards | Elasticsearch API compatible. Integrated with OpenSearch Dashboards (Kibana). Pairs with Kinesis, Firehose, CloudWatch for real-time log ingestion. |

---

#### **Exam Scenario Decision Matrix**

| If the Exam Scenario Asks For... | ...Your SAA-C03 Correct Answer Is | SAA-C03 Exam Trap / Key Warning |
| :--- | :--- | :--- |
| Scale SQL DB with minimum admin overhead + automatic storage scaling | **Aurora Serverless v2** | Don't choose EC2 with SQL if Aurora is available. |
| Cross-region DR with RTO < 1 min, RPO < 1 sec for relational DB | **Aurora Global Database** | Standard RDS RRs are async with higher replication lag. |
| Millisecond NoSQL latency at extreme scale with auto-active multi-region replication | **DynamoDB Global Tables** | Standard DynamoDB requires manual setup for multi-region; Global Tables automate it. |
| Microsecond database query times for read-heavy DynamoDB workloads | **DynamoDB Accelerator (DAX)** | ElastiCache can be used, but DAX requires no app code changes. |
| Speed up SQL queries on RDS (MySQL/PostgreSQL) with caching | **ElastiCache (Redis or Memcached)** | Cache lies *in front* of RDS. Do not use Redshift as a cache. |
| Durable Redis-compatible store that can act as the primary transactional database | **MemoryDB for Redis** | ElastiCache Redis is *not* durable enough to be the primary DB. |
| Analytical queries (OLAP) on huge datasets with SQL and BI tool integration | **Redshift** | Do not use RDS for OLAP; RDS is for OLTP. |
| Query petabytes of cold data in S3 directly using SQL without loading it first | **Redshift Spectrum** | S3 Select is for individual file queries; Athena is serverless ad-hoc; Redshift Spectrum is for warehouse integration. |
| Social network graph, fraud patterns, or path-finding relations | **Neptune** | Do not model deep relationships in SQL (slow joins) or NoSQL (no schema). |
| Cryptographically verifiable audit trail of all database edits | **QLDB** | Do not use standard SQL (which allows raw updates/deletes). QLDB is immutable. |
| High-rate sensor telemetry with auto-tiering to cold storage | **Timestream** | Saves cost by moving old data off SSDs automatically. |
| Elasticsearch API or Kibana log analysis and full-text search | **OpenSearch Service** | Use OpenSearch, not Neptune or Redshift. |

---