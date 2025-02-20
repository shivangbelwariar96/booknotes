# Assumptions & Inputs

| Metric                                      | Google Nearby Scale       | Uber Scale                  | Tinder Scale                   | How It’s Calculated                          |
|---------------------------------------------|----------------------------|-----------------------------|--------------------------------|----------------------------------------------|
| Active Users per Day                        | 500M+                      | 100M+                       | 75M+                           | Reported DAU (Daily Active Users)            |
| Proximity Checks per User/Hour              | 60 (once/min)              | 30 (rides)                  | 10 (swipes)                    | Estimated based on usage pattern             |
| Total Proximity Requests per Second (QPS)   | 8M+ RPS                    | 800K+ RPS                   | 200K+                          | Users × Checks per Hour ÷ 3600               |
| Avg. Request Payload Size                   | 1 KB                       | 2 KB                        | 500 B                          | Latitude, Longitude, Metadata                |

# Compute & Memory Requirements for Proximity Checks

| Component                                   | Google Nearby Scale       | Uber Scale                  | Tinder Scale                   | How It’s Calculated                          |
|---------------------------------------------|----------------------------|-----------------------------|--------------------------------|----------------------------------------------|
| Peak Requests Per Second (QPS)             | 8M RPS                     | 800K RPS                    | 200K RPS                       | Derived from proximity checks               |
| Response Time per Request                  | <50ms                      | <100ms                      | <150ms                          | Low latency requirement                      |
| Compute Nodes                               | 25K+ Servers               | 5K+ Servers                 | 2K+ Servers                    | Handles request processing                   |
| Memory per Server                          | 64 GB RAM                  | 32 GB RAM                   | 16 GB RAM                      | Needed for caching nearby results           |
| CPU Cores per Server                       | 16–32 Cores                | 8–16 Cores                  | 8 Cores                         | To process proximity algorithms             |

# Location Database (Geo Indexing & Storage)

| Storage Type                                | Google Nearby Scale       | Uber Scale                  | Tinder Scale                   | How It’s Calculated                          |
|---------------------------------------------|----------------------------|-----------------------------|--------------------------------|----------------------------------------------|
| Live Location Data (Hot Storage - Redis, Cassandra) | 10 PB                     | 2 PB                        | 500 TB                         | Stores recent location updates               |
| Historical Location Data (Cold Storage - BigQuery, S3)  | 500 PB                    | 100 PB                      | 10 PB                          | Stores past user locations                   |
| Geospatial Index (QuadTree, H3, S2)          | 5 PB                      | 1 PB                        | 250 TB                         | Used for fast proximity queries              |

# Caching Layer (Redis, Memcached, Edge Cache)

| Cache Type                                  | Google Nearby Scale       | Uber Scale                  | Tinder Scale                   | How It’s Calculated                          |
|---------------------------------------------|----------------------------|-----------------------------|--------------------------------|----------------------------------------------|
| Nearby Users Cache (Redis, 1-min TTL)       | 2 PB                       | 500 TB                      | 100 TB                         | Short-term storage for fast lookup           |
| Frequent Queries Cache (Memcached, 5-min TTL) | 500 TB                     | 100 TB                      | 25 TB                          | Popular proximity lookups                    |
| CDN Edge Cache (Proximity API Responses)    | 10 PB                      | 2 PB                        | 500 TB                         | Cached API responses for reduced latency    |

# Proximity Matching & Ranking

| Component                                   | Google Nearby Scale       | Uber Scale                  | Tinder Scale                   | How It’s Calculated                          |
|---------------------------------------------|----------------------------|-----------------------------|--------------------------------|----------------------------------------------|
| Proximity Algorithm                         | Haversine, QuadTree, KD-Tree | H3 Geo Index                | Haversine, R-Tree              | Used for efficient geolocation               |
| Matching Frequency                          | 1/sec per user             | 5/sec per ride request      | 1/sec per swipe                | How often matches are computed              |
| Matching Servers (ML & Heuristics)          | 10K+ Nodes                 | 2K+ Nodes                   | 500 Nodes                      | Handles load balancing                       |
| ML-Based Filtering (Spam Detection, Anomalies) | 5K GPUs                    | 1K GPUs                     | 250 GPUs                        | AI-based filtering                           |

# Network & API Infrastructure

| Infrastructure                               | Google Nearby Scale       | Uber Scale                  | Tinder Scale                   | How It’s Calculated                          |
|----------------------------------------------|----------------------------|-----------------------------|--------------------------------|----------------------------------------------|
| Total Network Throughput                     | 10+ Tbps                   | 2 Tbps                      | 500 Gbps                       | Estimated from payload sizes                |
| API Gateway Requests Per Second              | 8M+ RPS                    | 800K+ RPS                   | 200K+ RPS                      | User proximity queries                      |
| Number of Data Centers                       | 20+                        | 10+                         | 5+                             | Global redundancy                            |

# Cost Estimation (Cloud Equivalent Pricing)

| Cost Category                                | Google Nearby (Annual)    | Uber (Annual)               | Tinder (Annual)                | How It’s Calculated                          |
|----------------------------------------------|----------------------------|-----------------------------|--------------------------------|----------------------------------------------|
| Compute Costs                                | $1.5B+                     | $300M+                      | $100M+                          | Compute for location updates, AI            |
| Storage Costs                                | $2B+                       | $500M+                      | $200M+                          | Geo-index storage for real-time lookups     |
| Network Costs                                | $500M+                     | $100M+                      | $50M+                           | API traffic for proximity requests          |
| Total Annual Cost                            | $4B+                       | $900M+                      | $350M+                          | Sum of all above                            |
