# Assumptions and Inputs

| Metric                          | Facebook (~3B Users) | Twitter (~450M Users) | Instagram (~2B Users) | How It's Calculated |
|---------------------------------|----------------------|----------------------|----------------------|----------------------|
| **Total Users**                 | 3B                  | 450M                 | 2B                  | Public user count data |
| **Daily Active Users (DAU)**     | 2B                  | 250M                 | 1.5B                | Estimated ~60-70% of total users are active daily |
| **Avg. Feed Requests/User/Day**  | 30                  | 50                   | 40                  | Users check feeds multiple times a day |
| **Total Feed Requests/Day**      | 60B                 | 12.5B                | 60B                 | DAU × Avg. Requests/User |
| **Avg. Feed Response Size**      | 200 KB              | 100 KB               | 150 KB              | Twitter has mostly text; FB/IG have richer media |
| **Total Data Served/Day**        | 12 PB               | 1.25 PB              | 9 PB                | Total Requests × Avg. Response Size |
| **New Posts Per Second**         | 400K                | 50K                  | 250K                | Estimated posting rates based on engagement |
| **Storage per Post**             | 5 KB                | 3 KB                 | 4 KB                | Includes metadata, text; images/videos stored separately |
| **Total Storage for Posts/Day**  | 5 PB                | 500 TB               | 4 PB                | New Posts/sec × Post Size × 86400 (seconds/day) |

---

# Compute and Memory Requirements

| Component                     | Facebook  | Twitter | Instagram | How It's Calculated |
|--------------------------------|----------|---------|-----------|----------------------|
| **Peak QPS (Queries Per Second)** | 700K  | 100K   | 500K      | Total Requests/Day ÷ Seconds per Day |
| **Avg. Response Time**         | <100ms   | <50ms   | <80ms     | Twitter is faster due to smaller data size |
| **Database Servers**           | 20K      | 2K      | 15K       | Each DB server handles ~35K-50K queries per second |
| **Cache Size**                 | 2 PB     | 200 TB  | 1.5 PB    | Assumed 10-20% of active user data cached |
| **Application Servers**        | 50K      | 10K     | 40K       | Each app server handles ~10K-15K requests per second |
| **Total Network Bandwidth**    | 5 Tbps   | 500 Gbps| 3 Tbps    | Total Data Served ÷ Seconds per Day × 8 |

---

# Storage and Database Considerations

| Storage Type            | Facebook  | Twitter | Instagram | How It's Calculated |
|-------------------------|----------|---------|-----------|----------------------|
| **Hot Storage (SSDs, NVMe)**  | 2 PB  | 200 TB | 1.5 PB    | Assume 40% of active user data is in SSDs |
| **Warm Storage (HDDs, Object Storage)** | 3 PB  | 300 TB | 2.5 PB    | Remaining frequently accessed data |
| **Cold Storage (Archived Data)** | 10 PB | 1 PB   | 7 PB      | Older, rarely accessed posts |

---

# Caching Layer (Redis, Memcached, etc.)

| Cache Type              | Facebook  | Twitter | Instagram | How It's Calculated |
|-------------------------|----------|---------|-----------|----------------------|
| **User Feed Cache**     | 1 PB     | 100 TB  | 800 TB    | Stores recent posts for faster retrieval |
| **Post Cache**          | 500 TB   | 50 TB   | 400 TB    | 10% of active data stored in cache |
| **Cache Expiry (TTL)**  | 1-5 min  | 1 min   | 2-3 min   | Frequent updates needed |

---

# Backend Services and Worker Nodes

| Component                      | Facebook  | Twitter | Instagram | How It's Calculated |
|--------------------------------|----------|---------|-----------|----------------------|
| **Feed Generation Workers**    | 30K      | 5K      | 20K       | Parallel processing of requests |
| **Streaming Data Processing**  | 10K      | 2K      | 8K        | High throughput required for real-time ranking |
| **Machine Learning for Feed Ranking** | 15K GPUs | 5K GPUs | 12K GPUs  | AI-based ranking models |
| **API Gateway & Load Balancers** | 5K   | 1K    | 3K        | Load balancers handle 10-20% overhead |

---

# Network & Infrastructure

| Infrastructure         | Facebook  | Twitter | Instagram | How It's Calculated |
|-----------------------|----------|---------|-----------|----------------------|
| **Total Network Throughput** | 5 Tbps  | 500 Gbps | 3 Tbps    | Total Data Served ÷ 86400 × 8 |
| **Number of Data Centers**  | 20+     | 8       | 15        | Distributed for redundancy |
| **Edge CDN Servers**        | 10K     | 2K      | 7K        | Reducing latency by caching |

---

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category         | Facebook (Annual) | Twitter | Instagram | How It's Calculated |
|----------------------|------------------|---------|-----------|----------------------|
| **Compute (AWS/GCP/Azure)** | $2B   | $300M  | $1.5B      | Based on cloud instance costs |
| **Storage Costs**    | $1.2B  | $150M  | $900M       | S3/EBS storage cost estimates |
| **Network Costs**    | $500M  | $75M   | $400M       | Based on outbound data pricing |
| **Total Cost Estimate** | $3.7B  | $525M  | $2.8B       | Sum of all above |


