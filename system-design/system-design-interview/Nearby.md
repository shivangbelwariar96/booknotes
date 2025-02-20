# Assumptions & Inputs

| Metric                                      | Facebook Scale (~3B Users) | Snapchat Scale (~500M Users) | Apple Find My Scale (~1.5B Devices) | How It’s Calculated                          |
|---------------------------------------------|----------------------------|------------------------------|------------------------------------|----------------------------------------------|
| Active Users per Day (DAU)                 | 1.5B                       | 250M                         | 800M                               | Public data & DAU estimates                  |
| Proximity Checks per User/Hour             | 60 (once per min)          | 30 (less frequent)           | 10 (on-demand)                    | Usage pattern estimates                      |
| Total Proximity Requests/Second (QPS)      | 25M+ RPS                   | 2M+ RPS                      | 1.5M+                             | DAU × Checks per Hour ÷ 3600                 |
| Avg. Request Payload Size                  | 1 KB                       | 1 KB                         | 2 KB                               | Latitude, Longitude, Metadata                |

# Compute & Memory Requirements for Proximity Checks

| Component                                    | Facebook Scale             | Snapchat Scale               | Apple Find My Scale              | How It’s Calculated                          |
|----------------------------------------------|----------------------------|------------------------------|----------------------------------|----------------------------------------------|
| Peak Requests Per Second (QPS)              | 25M RPS                    | 2M RPS                       | 1.5M RPS                         | Derived from proximity checks               |
| Response Time per Request                  | <50ms                      | <100ms                       | <150ms                           | Low-latency requirement                      |
| Compute Nodes                                | 40K+ Servers               | 5K+ Servers                  | 3K+ Servers                      | Handles request processing                   |
| Memory per Server                           | 128 GB RAM                 | 64 GB RAM                    | 32 GB RAM                        | Needed for caching nearby results           |
| CPU Cores per Server                        | 32-64 Cores                | 16-32 Cores                  | 8-16 Cores                       | To process proximity algorithms             |

# Location Database (Geo Indexing & Storage)

| Storage Type                                 | Facebook Scale             | Snapchat Scale               | Apple Find My Scale              | How It’s Calculated                          |
|----------------------------------------------|----------------------------|------------------------------|----------------------------------|----------------------------------------------|
| Live Location Data (Hot Storage - Redis, Cassandra, DynamoDB) | 20 PB                      | 3 PB                         | 5 PB                             | Stores recent location updates               |
| Historical Location Data (Cold Storage - BigQuery, S3, HDFS)   | 1 EB                       | 150 PB                       | 200 PB                           | Stores past user locations                   |
| Geospatial Index (QuadTree, H3, S2)           | 10 PB                      | 2 PB                         | 3 PB                             | Used for fast proximity queries              |

# Caching Layer (Redis, Memcached, Edge Cache)

| Cache Type                                  | Facebook Scale             | Snapchat Scale               | Apple Find My Scale              | How It’s Calculated                          |
|---------------------------------------------|----------------------------|------------------------------|----------------------------------|----------------------------------------------|
| Nearby Friends Cache (Redis, 1-min TTL)     | 4 PB                       | 500 TB                       | 1 PB                             | Short-term storage for fast lookup           |
| Frequent Queries Cache (Memcached, 5-min TTL) | 1 PB                       | 250 TB                       | 500 TB                           | Popular proximity lookups                    |
| CDN Edge Cache (Proximity API Responses)    | 20 PB                      | 3 PB                         | 6 PB                             | Cached API responses for reduced latency    |

# Proximity Matching & Ranking

| Component                                   | Facebook Scale             | Snapchat Scale               | Apple Find My Scale              | How It’s Calculated                          |
|---------------------------------------------|----------------------------|------------------------------|----------------------------------|----------------------------------------------|
| Proximity Algorithm                         | Haversine, QuadTree, KD-Tree | H3 Geo Index                 | S2 Cell Indexing                 | Used for geolocation lookups                 |
| Matching Frequency                          | 1/sec per user             | 5/sec per snap update        | 1/min per location update        | How often matches are computed              |
| Matching Servers (ML & Heuristics)          | 20K+ Nodes                 | 3K+ Nodes                    | 5K+ Nodes                        | Handles load balancing                       |
| ML-Based Filtering (Spam Detection, Anomalies) | 10K GPUs                   | 1K GPUs                       | 2K GPUs                          | AI-based filtering                           |

# Network & API Infrastructure

| Infrastructure                               | Facebook Scale             | Snapchat Scale               | Apple Find My Scale              | How It’s Calculated                          |
|----------------------------------------------|----------------------------|------------------------------|----------------------------------|----------------------------------------------|
| Total Network Throughput                     | 20+ Tbps                   | 2.5 Tbps                     | 4 Tbps                            | Estimated from payload sizes                |
| API Gateway Requests Per Second              | 25M+ RPS                   | 2M+ RPS                      | 1.5M+ RPS                        | User proximity queries                      |
| Number of Data Centers                       | 30+                        | 10+                          | 15+                              | Global redundancy                            |

# Cost Estimation (Cloud Equivalent Pricing)

| Cost Category                                | Facebook (Annual)          | Snapchat (Annual)            | Apple Find My (Annual)           | How It’s Calculated                          |
|----------------------------------------------|----------------------------|------------------------------|----------------------------------|----------------------------------------------|
| Compute Costs                                | $2B+                       | $400M+                       | $700M+                           | Compute for location updates, AI            |
| Storage Costs                                | $3B+                       | $800M+                       | $1B+                             | Geo-index storage for real-time lookups     |
| Network Costs                                | $800M+                     | $200M+                       | $400M+                           | API traffic for proximity requests          |
| Total Annual Cost                            | $5.8B+                     | $1.4B+                       | $2.1B+                           | Sum of all above                            |
