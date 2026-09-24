### **Amazon ElastiCache - Managed In-Memory Datastore**

| Core Concept | Definition | Key Use Cases |
| :--- | :--- | :--- |
| **Amazon ElastiCache** | Fully managed in-memory key-value store compatible with Redis and Memcached. | • DB read offloading<br>• User session state storage<br>• Rate limiting & API throttling<br>• Real-time leaderboards (Redis) |

---

### **ElastiCache — Caching Strategies**

| Strategy | How it Works | Best For | Risk / Trade-off |
| :--- | :--- | :--- | :--- |
| **Lazy Loading**<br>*(Cache-Aside)* | 1. App checks cache.<br>2. On hit: returns data.<br>3. On miss: fetches from DB → writes to cache → returns. | Read-heavy workloads; tolerance for stale data. | • Cache miss penalty (3 network trips on miss).<br>• Stale data risk if DB updates without cache eviction. |
| **Write-Through** | 1. App writes data to DB.<br>2. App writes data to cache simultaneously. | Data freshness critical; small or highly active datasets. | • Write penalty (2 writes per write operation).<br>• Cache churn (stores infrequently read data). |
| **Write-Behind**<br>*(Write-Back)* | 1. App writes to cache first.<br>2. Background async task writes data to DB. | Write-heavy workloads; write coalescing. | • Data loss risk if cache node fails before DB write completes. |
| **Cache TTL**<br>*(Time-To-Live)* | 1. Keys are assigned expiration time (N seconds).<br>2. Key is automatically evicted after TTL expires. | Mitigating stale data; automatic cache cleanup. | • Too short TTL: higher DB load (frequent misses).<br>• Too long TTL: increased stale data exposure. |

---

### **Redis vs. Memcached**

| Feature | Redis | Memcached |
| :--- | :--- | :--- |
| **Data Structures** | Strings, Lists, Sets, Sorted Sets (Leaderboards), Hashes, Geospatial indexes, HyperLogLogs. | Simple Key-Value only (Strings, serialized objects up to 1MB). |
| **Architecture** | Single-threaded core (predictable latency, no locking overhead). | Multi-threaded (scales horizontally across multi-core CPUs). |
| **High Availability** | Supported (Multi-AZ with Auto-Failover). | Not supported (no built-in replication or Auto-Failover). |
| **Replication** | Supported (up to 5 Read Replicas (RRs) per primary node). | Not supported. |
| **Persistence** | Supported (AOF files, RDB snapshots). | Purely in-memory (no persistence; data lost on node reboot). |
| **Backup & Restore** | Supported (native snapshots exported to S3). | Not supported. |
| **Security** | VPC SGs, Redis AUTH (token), IAM Auth (v6.x+), TLS encryption. | VPC SGs, SASL authentication. |
| **Advanced Features** | Pub/Sub messaging, Lua scripting, Transactions. | Not supported. |

---

### **Redis Replication & Scaling Models**

| Model | Primary Nodes | Max Read Replicas (RRs) | Scaling Capability | Primary Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **Cluster Mode Disabled** | 1 Primary Node | Up to 5 RRs per Shard | Scale reads horizontally by adding RRs; scale writes vertically by upgrading node size. | Standard workloads with moderate write volumes and high read demands. |
| **Cluster Mode Enabled** | 1 to 500 Shards | Up to 5 RRs per Shard | Scale reads and writes horizontally by sharding data across multiple partitions. | Massive datasets requiring horizontal write scaling and ultra-low latency. |

---

### **Security & Deployments**

| Security Pillar | Mechanism | AWS SAA-C03 Implementation |
| :--- | :--- | :--- |
| **Network Security** | VPC Security Groups (SGs) | • Deploy ElastiCache in Private Subnets.<br>• Restrict SG ingress to allow traffic ONLY from application Tier SGs on port 6379 (Redis) or 11211 (Memcached). |
| **Data Encryption** | Encryption at Rest & In-Transit | • Rest: KMS managed keys.<br>• Transit: TLS-enabled connection endpoints (forces encryption of data over the network). |
| **Access Control** | Authentication (Auth) | • Redis AUTH: Token-based client validation.<br>• IAM Auth: Fine-grained AWS user and role authentication (Redis 6.x+). |

---

### **Exam Traps & Study Scenarios**

| ⚠️ EXAM TRAP: Redis vs Memcached for the exam: If the question mentions ANY of these → choose Redis: persistence, pub/sub, Lua scripting, sorted sets (leaderboards), geospatial, replication, Multi-AZ, backups, or complex data structures. Memcached is only chosen for 'simple, stateless cache needing horizontal scale with multiple CPU cores.' |
| ------ |

| ⚠️ EXAM TRAP: Database Offloading Patterns |
| :--- |
| • **Reducing RDS Read Load**: Place ElastiCache (either engine) in front of RDS to intercept and cache frequent read-heavy queries.<br>• **Session Store**: Deploy Redis (with Multi-AZ) to store web-tier session states, allowing EC2 instances to remain stateless and auto-scale dynamically.<br>• **Mitigating Thundering Herd**: Use TTL with added random jitter or pre-warm the cache before major traffic spikes to avoid simultaneous cache misses overwhelming the backend DB. |