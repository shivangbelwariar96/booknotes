# Assumptions & Inputs

| Metric                                    | AWS S3       | Google Cloud Storage (GCS) | Azure Blob Storage | How It’s Calculated                             |
|-------------------------------------------|--------------|----------------------------|--------------------|-------------------------------------------------|
| Total Data Stored                         | 300+ Exabytes| 100+ Exabytes              | 80+ Exabytes       | Publicly available reports                      |
| Total Requests per Day                    | 1+ Trillion  | 500B+                      | 400B+              | API requests from customers worldwide           |
| Peak Requests per Second (QPS)            | 100M+        | 50M+                       | 40M+               | Requests per day ÷ 86,400 sec                   |
| Avg. Object Size                          | 10 MB        | 8 MB                       | 6 MB               | Estimated based on mix of large and small objects |
| New Objects Uploaded Per Day              | 5 Trillion   | 2.5 Trillion               | 2 Trillion         | From applications, backups, and media uploads  |
| Total Data Ingested Per Day               | 50+ PB       | 20 PB                      | 15 PB              | New Objects × Avg. Object Size                  |
| Read vs. Write Ratio                      | 80% Reads / 20% Writes | 75% Reads / 25% Writes  | 70% Reads / 30% Writes | Based on usage patterns                      |

# Compute and Memory Requirements

| Component                                 | AWS S3       | GCS                        | Azure Blob Storage | How It’s Calculated                             |
|-------------------------------------------|--------------|----------------------------|--------------------|-------------------------------------------------|
| Peak QPS (Queries Per Second)             | 100M+        | 50M+                       | 40M+               | Requests per day ÷ 86,400 sec                   |
| Avg. API Latency (Read)                   | <50ms        | <80ms                      | <100ms             | Based on performance benchmarks                 |
| Avg. API Latency (Write)                  | <100ms       | <120ms                     | <150ms             | Write operations require replication overhead   |
| Storage Processing Nodes                  | 500K+        | 200K+                      | 150K+              | Handles metadata operations and indexing        |
| Metadata Database Servers                 | 100K+        | 50K+                       | 40K+               | Each DB server handles ~10K QPS                 |
| Cache Servers (Redis/Memcached)           | 50K+         | 25K+                       | 20K+               | Caching frequently accessed metadata            |
| Total Network Bandwidth                   | 20+ Tbps     | 10 Tbps                    | 8 Tbps             | Data ingress + egress traffic                   |

# Storage and Data Distribution

| Storage Type                              | AWS S3       | GCS                        | Azure Blob Storage | How It’s Calculated                             |
|-------------------------------------------|--------------|----------------------------|--------------------|-------------------------------------------------|
| Total Storage                             | 300+ EB      | 100+ EB                    | 80+ EB             | Based on total stored objects                   |
| Hot Storage (SSDs, NVMe)                  | 30 PB        | 15 PB                      | 10 PB              | For frequently accessed files                   |
| Warm Storage (HDDs, Object Storage)       | 1 EB         | 500 PB                     | 400 PB             | For moderately accessed data                    |
| Cold Storage (Glacier, Archive)           | 200+ EB      | 50 EB                      | 40 EB              | Rarely accessed, long-term backup               |
| Data Replication Factor                   | 3x - 6x      | 3x                         | 3x                 | Ensuring redundancy and durability              |

# Caching Layer (Metadata, File Access, API Requests)

| Cache Type                                | AWS S3       | GCS                        | Azure Blob Storage | How It’s Calculated                             |
|-------------------------------------------|--------------|----------------------------|--------------------|-------------------------------------------------|
| Metadata Index Cache                      | 5 PB         | 2 PB                       | 1 PB               | Stores frequently accessed metadata             |
| API Response Cache                        | 1 PB         | 500 TB                     | 300 TB             | Caching frequent GET/HEAD requests              |
| Cache Expiry (TTL)                        | 1-10 min     | 5-15 min                   | 10-20 min          | Lower TTL for frequently accessed metadata      |

# Backend Services and Worker Nodes

| Component                                 | AWS S3       | GCS                        | Azure Blob Storage | How It’s Calculated                             |
|-------------------------------------------|--------------|----------------------------|--------------------|-------------------------------------------------|
| Storage Indexing Workers                  | 50K+         | 25K+                       | 20K+               | Processes and updates object indexes            |
| Data Replication Workers                  | 100K+        | 50K+                       | 40K+               | Replicates data across availability zones       |
| Compression & Deduplication               | 50K GPUs     | 20K GPUs                   | 15K GPUs           | Reducing storage footprint                      |
| API Gateway & Load Balancers              | 20K          | 10K                        | 7K                 | Handles API request distribution                |

# Network & Infrastructure

| Infrastructure                             | AWS S3       | GCS                        | Azure Blob Storage | How It’s Calculated                             |
|--------------------------------------------|--------------|----------------------------|--------------------|-------------------------------------------------|
| Total Network Throughput                   | 20 Tbps      | 10 Tbps                    | 8 Tbps             | Data ingress + egress traffic                   |
| Number of Data Centers                     | 50+          | 30+                        | 25+                | Spread across multiple geographies              |
| Edge CDN Servers                          | 50K+         | 20K+                       | 15K+               | To reduce latency for object retrieval          |

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category                              | AWS S3 (Annual) | GCS                        | Azure Blob Storage | How It’s Calculated                             |
|--------------------------------------------|-----------------|----------------------------|--------------------|-------------------------------------------------|
| Compute (AWS/GCP/Azure)                    | $5B             | $2B                        | $1.5B              | Based on cloud instance costs                  |
| Storage Costs                              | $10B            | $4B                        | $3B                | S3/EBS storage cost estimates                   |
| Network Costs                              | $2B             | $1B                        | $750M              | Based on outbound data pricing                 |
| Total Cost Estimate                        | $17B+           | $7B+                       | $5.25B+            | Sum of all above                               |
