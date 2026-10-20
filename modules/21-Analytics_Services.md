###  **Analytics Services**

| Service | Purpose | Key Detail |
| :--- | :--- | :--- |
| Athena | • Serverless SQL queries on S3 | • Pay-per-query ($5/TB scanned)<br>• Partitioning, compression, and columnar formats (Parquet/ORC) reduce cost<br>• Query Federation scans external databases without moving data |
| Redshift | • Managed petabyte-scale OLAP data warehouse | • Massively Parallel Processing (MPP)<br>• RA3 nodes scale compute and storage independently<br>• Redshift Spectrum queries S3 in-place<br>• Serverless option automates cluster provisioning |
| EMR (Elastic MapReduce) | • Managed big data framework (Hadoop, Spark, Hive, Presto) | • For large-scale data processing<br>• Transient clusters save cost by terminating after batch jobs<br>• Spot Instances on Task nodes optimize compute cost<br>• EMR Serverless automates scaling |
| Glue | • Serverless data integration and ETL | • Crawlers auto-discover schema and populate Data Catalog<br>• Data Catalog acts as a central metadata index<br>• Spark ETL code generated automatically |
| QuickSight | • Serverless BI and dashboarding | • SPICE in-memory engine accelerates visualization<br>• Pay-per-session pricing for readers<br>• ML insights, forecasting, and Q (NLP) features<br>• Row/column-level security |
| OpenSearch Service | • Log analytics and application search | • Successor to Amazon Elasticsearch Service<br>• OpenSearch Dashboards for visualization<br>• Serverless and managed cluster options |
| Lake Formation | • Secure S3 data lake setup and governance | • Centralized access control on Glue Data Catalog<br>• Column, row, and cell-level security<br>• Simplifies permissions across Athena, Redshift, and EMR |
| Kinesis | • Real-time streaming data ingestion and analysis | • **KDS**: Low-latency ingest; scalable shards; replayable data<br>• **KDF**: Serverless delivery to S3, Redshift, OpenSearch; transforms with Lambda<br>• **KDA**: Real-time SQL or Apache Flink analytics |
| Amazon MSK | • Fully managed Apache Kafka cluster | • Alternative to Kinesis for open-source compatibility<br>• High availability in multi-AZ deployments<br>• Integrates with AWS IAM and Glue Schema Registry |

--------------------------------------------------------------------------------
