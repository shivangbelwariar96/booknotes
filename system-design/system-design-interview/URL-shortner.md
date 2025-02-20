# Assumptions & Inputs

| Metric                                      | Google Firebase (Large Scale)  | Bit.ly (Mid Scale)           | TinyURL (Small Scale)         | How It’s Calculated                          |
|---------------------------------------------|--------------------------------|------------------------------|-------------------------------|----------------------------------------------|
| Total Users                                 | 1B+                            | 500M                         | 100M                          | Public user estimates                        |
| Daily Active Users (DAU)                    | 300M                           | 50M                          | 10M                           | 30-50% of total users active daily          |
| Avg. Shortened URLs/User                    | 10                             | 5                            | 3                             | Estimated per-user link creation            |
| Total Shortened URLs/Day                    | 3B                             | 250M                         | 30M                           | DAU × Avg. Shortened URLs/User              |
| Avg. Redirection Requests/Day               | 50B                            | 5B                           | 1B                            | Popular URLs are accessed more frequently  |
| Avg. URL Storage Size                       | 0.5 KB                         | 0.4 KB                       | 0.4 KB                        | Short URL + Metadata (timestamps, analytics)|

# Capacity Estimation for URL Shortener

| Metric                                      | Google Firebase Scale         | Bit.ly Scale                 | TinyURL Scale                 | How It’s Calculated                          |
|---------------------------------------------|--------------------------------|------------------------------|-------------------------------|----------------------------------------------|
| Peak QPS (Queries Per Second)               | 600M QPS                       | 50M QPS                      | 10M QPS                        | Total Requests/Day ÷ 86400 seconds         |
| Avg. Response Time                           | <5ms                           | <20ms                        | <50ms                          | Highly optimized for speed                  |
| Database Servers (PostgreSQL, DynamoDB, BigTable, etc.) | 5000+ nodes                | 1000+ nodes                  | 200+ nodes                     | Each DB node handles ~50K QPS               |
| Memory Usage (Hot Storage)                  | 1 PB                           | 500 TB                       | 100 TB                         | Active URL Metadata Cached in RAM           |
| Application Servers (API & Redirects)       | 50K+ servers                   | 10K servers                  | 2K servers                     | Handles link creation & redirects           |
| Total Storage for URLs/Day                  | 1.5 PB                         | 100 TB                       | 10 TB                          | Short URLs × Avg. URL Size                  |

# Caching Layer (Redis, Memcached, etc.)

| Cache Type                                  | Google Scale                  | Bit.ly Scale                 | TinyURL Scale                 | How It’s Calculated                          |
|---------------------------------------------|--------------------------------|------------------------------|-------------------------------|----------------------------------------------|
| Hot Cache (Popular Links)                   | 800 TB                         | 200 TB                       | 50 TB                          | Top 10% of URLs stored for fast lookup      |
| Distributed Cache (Redis, Memcached)        | 1 PB                           | 500 TB                       | 100 TB                         | Used to avoid DB queries                    |
| Cache Expiry (TTL)                          | 1-10 min                       | 1-30 min                     | 10-60 min                      | Older links expire from cache               |

# Backend Services and Worker Nodes

| Component                                   | Google Scale                  | Bit.ly Scale                 | TinyURL Scale                 | How It’s Calculated                          |
|---------------------------------------------|--------------------------------|------------------------------|-------------------------------|----------------------------------------------|
| Short URL Generation Workers                | 10K+                           | 5K+                          | 1K+                            | Parallel processing of link creation        |
| Redirection Service Nodes                   | 50K+                           | 10K+                         | 2K+                            | Handles fast URL lookup & redirect          |
| Analytics Processing Nodes                  | 5K+                            | 1K+                          | 200+                           | Logs click events & user interactions       |
| Spam & Abuse Detection (AI/ML)              | 5K GPUs                        | 2K GPUs                       | 500 GPUs                       | AI-based bot/spam filtering                 |

# Network & Infrastructure

| Infrastructure                               | Google Scale                  | Bit.ly Scale                 | TinyURL Scale                 | How It’s Calculated                          |
|----------------------------------------------|--------------------------------|------------------------------|-------------------------------|----------------------------------------------|
| Total Network Throughput                     | 10 Tbps                        | 2 Tbps                       | 500 Gbps                       | Handles redirections                         |
| Number of Data Centers                       | 20+                            | 8+                           | 5+                             | Distributed for redundancy                  |
| Edge CDN Servers                             | 10K+                           | 3K+                          | 1K+                            | Caches redirections                          |

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category                                | Google Scale (Annual)         | Bit.ly Scale (Annual)         | TinyURL Scale (Annual)         | How It’s Calculated                          |
|----------------------------------------------|--------------------------------|-------------------------------|--------------------------------|----------------------------------------------|
| Compute Costs                                | $1B+                           | $500M+                        | $200M+                         | Cloud instances & compute costs             |
| Storage Costs                                | $500M+                         | $250M+                        | $100M+                         | Object storage for URLs & metadata          |
| Network Costs                                | $250M+                         | $100M+                        | $50M+                          | Outbound data pricing                       |
| Total Annual Cost                            | $1.75B+                        | $850M+                        | $350M+                         | Sum of all above                            |
