# Assumptions & Inputs

| Metric                                      | Marriott (marriott.com) | Airbnb (airbnb.com) | Expedia (expedia.com) | How It’s Calculated                               |
|---------------------------------------------|-------------------------|----------------------|-----------------------|---------------------------------------------------|
| Total Listings                              | 8,500+ hotels           | 7M+ properties       | 2.9M hotels & properties | Publicly available stats                          |
| Total Registered Users                      | 100M+ users             | 400M+ users          | 500M+ users           | Based on company reports                          |
| Daily Active Users (DAU)                    | 5M users                | 50M users            | 30M users             | Estimated 10-20% of total user base               |
| Average Searches per User/Day               | 5 searches              | 15 searches          | 10 searches           | Users typically compare multiple hotels           |
| Total Searches Per Day                      | 25M searches/day        | 750M searches/day    | 300M searches/day     | DAU × Avg. Searches/User                          |
| Average Search Response Size                | 300 KB                  | 500 KB               | 400 KB                | Includes hotel details, images, pricing           |
| Total Data Served/Day                       | 7.5 TB                  | 375 TB               | 120 TB                | Total Searches × Response Size                    |

# Compute and Memory Requirements

| Component                                   | Marriott     | Airbnb      | Expedia     | How It’s Calculated                               |
|---------------------------------------------|-------------|-------------|-------------|---------------------------------------------------|
| Peak QPS (Queries Per Second)               | 300K QPS    | 8.7M QPS    | 3.5M QPS    | Total Searches/Day ÷ 86,400 sec                  |
| Average Response Time                       | <100ms      | <200ms      | <150ms      | Airbnb has more complex listings                  |
| Application Servers                         | 5,000       | 40,000      | 25,000      | Each app server handles ~10K QPS                  |
| Database Servers                            | 3,000       | 15,000      | 8,000       | Each DB server handles ~20K QPS                   |
| Cache Servers                               | 1,000       | 8,000       | 4,000       | Redis/Memcached for fast lookups                  |
| Total Network Bandwidth                     | 100 Gbps    | 1.5 Tbps    | 500 Gbps    | Total Data Served/Day × 8 ÷ 86,400                |

# Storage and Database Considerations

| Storage Type                                | Marriott     | Airbnb      | Expedia     | How It’s Calculated                               |
|---------------------------------------------|-------------|-------------|-------------|---------------------------------------------------|
| Hot Storage (SSDs, NVMe)                    | 500 TB      | 3 PB        | 1.5 PB      | 40% of active user data in SSDs                   |
| Warm Storage (HDDs, Object Storage)         | 1 PB        | 7 PB        | 4 PB        | Frequently accessed data                          |
| Cold Storage (Archived Data)                | 5 PB        | 20 PB       | 10 PB       | Old user history & records                        |

# Caching Layer (Redis, Memcached, etc.)

| Cache Type                                  | Marriott     | Airbnb      | Expedia     | How It’s Calculated                               |
|---------------------------------------------|-------------|-------------|-------------|---------------------------------------------------|
| Search Query Cache                          | 300 TB      | 2 PB        | 1 PB        | 50% of popular searches cached                    |
| User Session Cache                          | 100 TB      | 500 TB      | 250 TB      | Storing user preferences                          |
| Cache Expiry (TTL)                          | 10 min      | 5 min       | 8 min       | Frequent updates required                         |

# Backend Services and Worker Nodes

| Component                                   | Marriott     | Airbnb      | Expedia     | How It’s Calculated                               |
|---------------------------------------------|-------------|-------------|-------------|---------------------------------------------------|
| Booking Processing Workers                  | 2,000       | 10,000      | 5,000       | Parallel processing for transactions              |
| Data Processing & ML Models                 | 5,000 GPUs  | 50,000 GPUs | 20,000 GPUs  | AI for pricing, recommendations                   |
| API Gateway & Load Balancers                | 2,000       | 10,000      | 5,000       | Handles 10-20% overhead                            |

# Network & Infrastructure

| Infrastructure                               | Marriott     | Airbnb      | Expedia     | How It’s Calculated                               |
|----------------------------------------------|-------------|-------------|-------------|---------------------------------------------------|
| Total Network Throughput                     | 100 Gbps    | 1.5 Tbps    | 500 Gbps    | Total Data Served/Day × 8 ÷ 86,400                |
| Number of Data Centers                       | 5+          | 20+         | 10+         | Distributed globally for redundancy               |
| CDN Edge Servers                             | 3,000       | 15,000      | 7,000       | Reducing latency via caching                      |

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category                                | Marriott (Annual) | Airbnb      | Expedia     | How It’s Calculated                               |
|----------------------------------------------|-------------------|-------------|-------------|---------------------------------------------------|
| Compute (AWS/GCP/Azure)                      | $200M             | $2B         | $1B         | Based on cloud instance costs                    |
| Storage Costs                                | $50M              | $500M       | $250M       | S3/EBS storage cost estimates                     |
| Network Costs                                | $20M              | $300M       | $150M       | Based on outbound data pricing                   |
| Total Cost Estimate                          | $270M             | $2.8B       | $1.4B       | Sum of all above                                 |
