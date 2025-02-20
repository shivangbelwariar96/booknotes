# Assumptions & Inputs

| Metric                                    | Gmail (Google Mail) | Outlook (Microsoft Mail) | Yahoo Mail        | How It’s Calculated                             |
|-------------------------------------------|---------------------|--------------------------|-------------------|-------------------------------------------------|
| Total Users                               | 2B+ users           | 400M+ users              | 225M+ users       | Publicly available stats                        |
| Daily Active Users (DAU)                  | 1B users            | 250M users               | 125M users        | Estimated ~50% of total users active daily      |
| Avg. Emails Sent/User/Day                 | 15 emails           | 10 emails                | 8 emails          | Average email activity per user                 |
| Avg. Emails Received/User/Day             | 50 emails           | 40 emails                | 30 emails         | Includes newsletters, spam, promotions          |
| Total Emails Sent/Day                     | 15B emails/day      | 2.5B emails/day          | 1B emails/day     | DAU × Avg. Emails Sent                          |
| Total Emails Received/Day                 | 50B emails/day      | 10B emails/day           | 3.75B emails/day  | DAU × Avg. Emails Received                      |
| Avg. Email Size (Including Attachments)    | 150 KB              | 100 KB                   | 80 KB             | Includes text, headers, metadata                |
| Total Data Processed/Day                  | 9 PB/day            | 1 PB/day                 | 300 TB/day        | (Sent + Received Emails) × Avg. Email Size      |

# Compute and Memory Requirements

| Component                                 | Gmail       | Outlook     | Yahoo Mail   | How It’s Calculated                             |
|-------------------------------------------|-------------|-------------|--------------|-------------------------------------------------|
| Peak QPS (Queries Per Second)             | 800M QPS    | 200M QPS    | 100M QPS     | Emails Processed/Day ÷ 86,400 sec               |
| Average Response Time                     | <50ms       | <100ms      | <150ms       | Gmail is optimized for speed                    |
| Application Servers                       | 100K        | 25K         | 15K          | Each app server handles ~10K QPS                |
| Mail Processing Servers                   | 150K        | 35K         | 20K          | Includes filtering, AI classification           |
| Spam & Security Servers                   | 50K         | 10K         | 8K           | Handles virus scanning & spam detection         |
| Database Servers                          | 75K         | 20K         | 12K          | Each DB server handles ~20K QPS                |
| Cache Servers (Redis/Memcached)           | 20K         | 5K          | 3K           | Stores frequently accessed mailboxes            |
| Total Network Bandwidth                   | 5 Tbps      | 750 Gbps    | 250 Gbps     | Total Data Processed/Day × 8 ÷ 86,400           |

# Storage and Database Considerations

| Storage Type                              | Gmail       | Outlook     | Yahoo Mail   | How It’s Calculated                             |
|-------------------------------------------|-------------|-------------|--------------|-------------------------------------------------|
| Hot Storage (SSDs, NVMe)                  | 10 PB       | 3 PB        | 1 PB         | 40% of active user data stored in SSDs          |
| Warm Storage (HDDs, Object Storage)       | 50 PB       | 10 PB       | 5 PB         | Frequently accessed mail data                   |
| Cold Storage (Archived Data)              | 500 PB      | 100 PB      | 50 PB        | Old email history, archived data                |

# Caching Layer (Redis, Memcached, etc.)

| Cache Type                                | Gmail       | Outlook     | Yahoo Mail   | How It’s Calculated                             |
|-------------------------------------------|-------------|-------------|--------------|-------------------------------------------------|
| Active User Mailbox Cache                 | 5 PB        | 1 PB        | 500 TB       | Storing recent emails for fast access           |
| Spam & Security Cache                     | 500 TB      | 100 TB      | 50 TB        | Email filtering and blacklists                  |
| Cache Expiry (TTL)                        | 10 min      | 15 min      | 20 min       | Frequent updates required                       |

# Backend Services and Worker Nodes

| Component                                 | Gmail       | Outlook     | Yahoo Mail   | How It’s Calculated                             |
|-------------------------------------------|-------------|-------------|--------------|-------------------------------------------------|
| Mail Delivery Workers                     | 25K         | 5K          | 3K           | Parallel processing for delivery queues         |
| Spam Filtering & AI Models                | 50K GPUs    | 10K GPUs    | 5K GPUs      | AI models for spam detection                    |
| API Gateway & Load Balancers              | 10K         | 3K          | 2K           | Handles traffic load distribution               |

# Network & Infrastructure

| Infrastructure                             | Gmail       | Outlook     | Yahoo Mail   | How It’s Calculated                             |
|--------------------------------------------|-------------|-------------|--------------|-------------------------------------------------|
| Total Network Throughput                   | 5 Tbps      | 750 Gbps    | 250 Gbps     | Total Data Processed/Day × 8 ÷ 86,400           |
| Number of Data Centers                     | 20+         | 10+         | 5+           | Distributed globally for redundancy             |
| Edge CDN Servers                           | 10K         | 5K          | 2K           | Reducing latency for email retrieval            |

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category                              | Gmail (Annual) | Outlook     | Yahoo Mail   | How It’s Calculated                             |
|--------------------------------------------|----------------|-------------|--------------|-------------------------------------------------|
| Compute (AWS/GCP/Azure)                    | $3B            | $700M       | $500M        | Based on cloud instance costs                  |
| Storage Costs                              | $1.5B          | $400M       | $200M        | S3/EBS storage cost estimates                   |
| Network Costs                              | $1B            | $250M       | $100M        | Based on outbound data pricing                 |
| Total Cost Estimate                        | $5.5B          | $1.35B      | $800M        | Sum of all above                               |
