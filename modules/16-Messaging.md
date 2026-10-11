### **Messaging and Event-Driven Architecture**

| Core AWS Principle | Role of Messaging Services | SAA-C03 Exam Relevance |
| :--- | :--- | :--- |
| **Decoupling components** | Serves as architectural glue between decoupled system tiers (e.g., web, app, database) | High (Prevents cascading failures, enables independent scaling) |

| Service | Pattern | Key Traits | SAA-C03 Exam Scenario / Key Phrases |
| :--- | :--- | :--- | :--- |
| **SQS** | Queue (Point-to-Point pull) | Pull model; msgs stored up to 14 d; at-least-once delivery (Standard) or exactly-once (FIFO) | "decouple", "buffer requests", "worker pool", "async processing", "scale web/app tiers independently" |
| **SNS** | Pub/Sub (Push fan-out) | Push model; up to 12.5M subs/topic; no persistence; multi-protocol (SQS, Lambda, HTTP/S, email, SMS, mobile push) | "fan-out", "notify multiple services simultaneously", "event broadcasting", "pub/sub" |
| **EventBridge** | Event Bus (Pub/Sub + Rules) | Event routing via rules; integrates 200+ AWS services & third-party SaaS; schema registry; archiving & replay; API Destinations | "event-driven", "route events from AWS services", "SaaS integration", "trigger Lambda/Step Functions based on schedule or system state" |
| **Kinesis Data Streams** | Real-time streaming | Retain 24 hr - 365 d; multiple consumers; ordered within shard; replay capability; shard-based scaling | "real-time analytics", "clickstream", "IoT telemetry", "replay events", "multi-consumer data streams" |
| **Kinesis Data Firehose** | Streaming ETL delivery | Fully managed delivery to S3, Redshift, OpenSearch, Splunk; near-real-time (buffer: 60 sec - 900 sec, 1 MB - 128 MB) | "load streaming data to S3/Redshift without managing consumers", "transform + deliver", "zero administration streaming ingestion" |
| **Kinesis Data Analytics** | Real-time SQL on streams | Run SQL or Apache Flink on Kinesis/MSK streams; real-time aggregations & anomaly detection | "real-time SQL on streaming data", "running averages", "anomaly detection in real-time" |
| **MSK (Managed Kafka)** | Managed Apache Kafka | AWS-managed Kafka brokers; Kafka API compatible; persistent storage on EBS/Tiered storage | "migrate existing Kafka workloads", "Kafka API compatibility", "exactly-once semantics", "long retention" |
| **MQ (Amazon MQ)** | Managed message broker | ActiveMQ / RabbitMQ; for migrating on-prem apps using JMS, AMQP, MQTT, STOMP, WebSocket | "migrate from on-prem ActiveMQ/RabbitMQ", "enterprise messaging protocols (MQTT, STOMP, AMQP, JMS)" |

---

#### **SQS Deep Dive**

| Property / Feature | Standard SQS | FIFO SQS | SAA-C03 Exam Note |
| :--- | :--- | :--- | :--- |
| **Ordering** | Best-effort (no ordering guarantee) | Strict First-In-First-Out (FIFO) per Message Group ID | Order matters (e.g., transaction processing) -> FIFO |
| **Delivery** | At-least-once (duplicates possible) | Exactly-once processing (deduplication) | Duplicate prevention -> FIFO SQS |
| **Throughput** | Nearly unlimited API transactions/sec | 300 msg/sec standard (3,000 with batching)<br>With **High Throughput FIFO**: Up to 3,000 msg/sec (30,000 with batching) | Use High Throughput FIFO if standard FIFO limit is exceeded |
| **Deduplication** | Not supported | 5-min deduplication window (Content-Based or explicit Message Deduplication ID) | Prevents processing identical messages within 5 min |
| **Use Case** | High throughput, order-agnostic processing | Order fulfillment, financial transactions, booking systems | Match use case strictly to order/deduplication requirements |
| **Queue Name** | Any valid name | Must end with `.fifo` suffix | Strict naming convention is tested on SAA-C03 |

##### **SQS Important Properties**

| Property | Default Value | Limit Range (Min - Max) | Architectural Purpose & SAA-C03 Impact |
| :--- | :--- | :--- | :--- |
| **Visibility Timeout** | 30 sec | 0 sec - 12 hr | Prevents other consumers from processing a msg currently being worked on. If consumer fails, msg reappears after timeout. |
| **Message Retention** | 4 d | 60 sec - 14 d | Duration msgs persist in the queue if not deleted by a consumer. |
| **Message Size** | 256 KB | Max 256 KB | For payloads > 256 KB up to 2 GB, use SQS Extended Client Library with Amazon S3 (SQS stores S3 pointer). |
| **Long Polling** | 0 sec (Short Poll) | 1 sec - 20 sec | SQS waits up to 20 sec for msgs to arrive before returning empty response. **Best practice** to reduce empty API calls, lower cost, and decrease CPU polling spikes. |
| **Dead Letter Queue (DLQ)** | Disabled | — | Target queue for msgs failing processing `maxReceiveCount` times. Used for isolating & debugging broken msgs. |
| **Delay Queue** | 0 sec | 0 sec - 15 min | Delays delivery of new msgs to consumers by N sec. Useful for giving dependent databases or processes time to update. |

| 💡 **TIP: SQS Visibility Timeout Troubleshooting** |
| :--- |
| **Visibility Timeout Confusion:** If a consumer crashes while processing, the visibility timeout must expire before the msg becomes visible again in SQS. **SAA-C03 Solution:** If consumers need more time, use `ChangeMessageVisibility` API. If other consumers are grabbing msgs too early (causing duplicate processing), increase the Visibility Timeout to exceed the maximum processing time. |

---

#### **SNS vs SQS vs EventBridge — When to Choose**

| Scenario-Based Architectural Goal | SAA-C03 Optimal Service Choice | Integration details |
| :--- | :--- | :--- |
| **"Fan-out to multiple services simultaneously"**<br>(e.g., Order placed -> notify Billing, Shipping, & Inventory) | **SNS Fan-out Pattern** | SNS Topic -> fans out to multiple SQS queues (each subscriber queue processes independently). |
| **"Decouple producer from consumer / buffer traffic spikes"** | **SQS Queue** | Buffer msgs to protect downstream databases or EC2 worker pools from crash. |
| **"Route events from 200+ AWS services or SaaS apps with complex filter rules"** | **EventBridge Event Bus** | Serverless event bus with advanced rule-based JSON filtering, schema registry, and target routing. |
| **"Real-time stream processing, replay events, multiple consumers with dedicated throughput"** | **Kinesis Data Streams** | Ordered processing within shards, replay capability, Enhanced Fan-out (2 MB/s dedicated read pipe per consumer). |
| **"Deliver streaming data to S3, Redshift, OpenSearch, Splunk without managing consumers"** | **Kinesis Data Firehose** | Fully managed near-real-time streaming ETL, zero infrastructure management, auto-compresses & buffers. |
| **"Migrate existing ActiveMQ/RabbitMQ to managed service"** | **Amazon MQ** | Direct drop-in migration for industry-standard JMS/AMQP/MQTT/STOMP/WebSocket protocols. |
| **"Kafka workload needing Kafka API compatibility & custom clustering"** | **MSK (Managed Streaming for Kafka)** | AWS-managed Apache Kafka cluster, integrates with native Kafka tooling, EBS storage. |

---

#### **Kinesis Shards & Scaling**

| Kinesis Streaming Feature | Capacity / Behavior | SAA-C03 Scaling & Performance Guidelines |
| :--- | :--- | :--- |
| **Shard Capacity** | Write: 1 MB/sec (or 1,000 msg/sec)<br>Read: 2 MB/sec per shard | **Scale-Up**: Shard Split (splits 1 hot shard into 2).<br>**Scale-Down**: Shard Merge (combines 2 cold shards).<br>If limits exceeded: `ProvisionedThroughputExceeded` exception. |
| **Enhanced Fan-Out** | Dedicated 2 MB/sec read pipe per consumer | By default, consumers share 2 MB/sec read capacity per shard. Use Enhanced Fan-out to give each consumer independent 2 MB/sec read pipe (lowers latency to < 70 ms, removes consumer competition). |

| 🧠 **MNEMONIC: Kinesis vs Firehose vs Analytics** |
| :--- |
| **Kinesis Data Streams:** Real-time + Replay + Multiple consumers. Needs manual producer/consumer management.<br>**Kinesis Data Firehose:** Streaming ingestion + Near-real-time (min 60 sec buffer) + Auto-load to S3/Redshift/OpenSearch. Fully managed (no consumers to write).<br>**Kinesis Data Analytics:** SQL/Flink on active streams to calculate running averages, metrics, and detect anomalies on the fly. |

---

