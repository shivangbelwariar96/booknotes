# Assumptions & Inputs

| Metric                                       | Google Scale (~10B Requests/Day) | Instagram Scale (~5B Requests/Day) | Twitter Scale (~2B Requests/Day) | How It’s Calculated                                      |
|----------------------------------------------|----------------------------------|-----------------------------------|----------------------------------|----------------------------------------------------------|
| Total Users                                  | 2B                               | 1.5B                              | 500M                             | Public user estimates                                    |
| Daily Active Users (DAU)                     | ~1B                              | ~1B                               | ~300M                            | Estimated 50-70% of total users active daily              |
| Avg. API Requests/User/Day                   | ~10K requests                    | ~5K requests                      | ~3K requests                     | API-heavy applications require frequent rate limiting    |
| Total Requests/Day                           | ~10B                             | ~5B                               | ~2B                              | DAU × Avg. Requests/User                                 |
| Avg. Rate Limit (RPS/user)                    | 100-1000 RPS                     | 50-500 RPS                        | 10-100 RPS                       | Application-specific API rate limits                     |

# Capacity Estimation for Rate Limiting System

| Metric                                       | Google Scale                      | Instagram Scale                   | Twitter Scale                     | How It’s Calculated                                      |
|----------------------------------------------|----------------------------------|-----------------------------------|----------------------------------|----------------------------------------------------------|
| Peak QPS (Queries Per Second)                | 150M QPS                          | 75M QPS                           | 25M QPS                           | Total Requests/Day ÷ 86400 seconds                      |
| Avg. Response Time                           | <10ms                             | <20ms                             | <50ms                             | Optimized for low-latency checks                         |
| Rate Limiting DB Servers                     | 1000+ nodes                       | 500 nodes                         | 200 nodes                         | Based on Redis Cluster / DynamoDB / Google Spanner      |
| Memory Usage (Rate Limit State)              | 5 PB                              | 2.5 PB                            | 1 PB                              | Peak QPS × Request State Size                            |
| Application Servers (API Gateways)           | 50K+ servers                      | 25K servers                       | 10K servers                       | Handles incoming API traffic                             |
| Total Storage for Logs/Day                   | 10 PB                             | 5 PB                              | 2 PB                              | Logging requests for security & analysis                 |

# Caching Layer (Redis, Memcached, etc.)

| Cache Type                                   | Google Scale                      | Instagram Scale                   | Twitter Scale                     | How It’s Calculated                                      |
|----------------------------------------------|----------------------------------|-----------------------------------|----------------------------------|----------------------------------------------------------|
| Rate Limit State Cache                       | ~2 PB                             | ~1 PB                             | ~500 TB                           | Stores temporary rate limit state                         |
| User Authentication Cache                    | ~500 TB                           | ~200 TB                           | ~100 TB                           | Cached user tokens for rate checks                        |
| Cache Expiry (TTL)                           | 1-10 sec                          | 1-60 sec                          | 10-120 sec                        | Varies based on API endpoint type                        |
| Request Throttling Cache                     | 1 PB                              | 500 TB                            | 250 TB                            | Tracks historical request patterns                       |

# Backend Services and Worker Nodes

| Component                                    | Google Scale                      | Instagram Scale                   | Twitter Scale                     | How It’s Calculated                                      |
|----------------------------------------------|----------------------------------|-----------------------------------|----------------------------------|----------------------------------------------------------|
| Rate Limiter Service Nodes                   | 20K+                              | 10K+                              | 5K+                               | Parallel processing of rate limit checks                 |
| Traffic Shaping Workers                      | 10K+                              | 5K+                               | 2K+                               | Applies request throttling                               |
| Logging & Monitoring Servers                 | 5K+                               | 2K+                               | 1K+                               | Stores logs for abuse detection                          |
| AI-based Anomaly Detection                   | 5K GPUs                           | 2K GPUs                            | 1K GPUs                           | Uses ML models to detect abuse                           |

# Network & Infrastructure

| Infrastructure                                | Google Scale                      | Instagram Scale                   | Twitter Scale                     | How It’s Calculated                                      |
|-----------------------------------------------|----------------------------------|-----------------------------------|----------------------------------|----------------------------------------------------------|
| Total Network Throughput                      | 10 Tbps                           | 5 Tbps                            | 2 Tbps                            | Total requests handled per second                        |
| Number of Data Centers                        | 20+                               | 10+                               | 5+                                | Distributed for redundancy                               |
| Edge CDN Servers                              | 10K+                              | 5K+                               | 3K+                               | Caches responses for rate limit failures                 |

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category                                 | Google Scale (Annual)             | Instagram Scale (Annual)           | Twitter Scale (Annual)            | How It’s Calculated                                      |
|-----------------------------------------------|----------------------------------|-----------------------------------|----------------------------------|----------------------------------------------------------|
| Compute Costs                                 | $3B                              | $1.5B                              | $700M                             | Based on cloud pricing for large-scale distributed compute |
| Storage Costs                                 | $2B                              | $1B                                | $500M                             | S3/EBS equivalent pricing for logs & cached states        |
| Network Costs                                 | $500M                            | $250M                              | $100M                             | Based on outbound traffic pricing                       |
| Total Annual Cost                             | $5.5B+                           | $2.75B+                            | $1.3B+                            | Sum of all above                                          |
