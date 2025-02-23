# Assumptions and Inputs

| Metric                      | WhatsApp (~2.5B Users) | Messenger (~1.3B Users) | Telegram (~800M Users) | How It's Calculated |
|-----------------------------|-----------------------|------------------------|-----------------------|---------------------|
| **Total Users**             | 2.5B                  | 1.3B                   | 800M                  | Publicly available user base data |
| **Daily Active Users (DAU)** | 1.75B                 | 800M                   | 500M                  | Assumed ~60-70% of total users are active daily |
| **Avg. Messages/User/Day**   | 50                    | 40                     | 60                    | WhatsApp & Telegram see higher engagement |
| **Total Messages/Day**      | 87.5B                 | 32B                    | 30B                   | DAU × Avg. Messages/User |
| **Avg. Message Size**       | 3 KB                  | 2.5 KB                 | 3.5 KB                | Includes text, metadata (without media) |
| **Total Data Sent/Day**     | 260 PB                | 80 PB                  | 105 PB                | Total Messages × Avg. Size |
| **Avg. Media Uploads/User/Day** | 5                  | 4                      | 6                      | Shared images, videos, voice notes |
| **Avg. Media Size**         | 500 KB                | 400 KB                  | 600 KB                | WhatsApp uses stronger compression |
| **Total Media Uploads/Day** | 8.75B                 | 3.2B                   | 3B                    | DAU × Avg. Media Uploads/User |
| **Total Media Storage/Day** | 4.3 PB                | 1.3 PB                 | 1.8 PB                | Total Media × Avg. Size |

---

# Compute and Memory Requirements

| Component                  | WhatsApp | Messenger | Telegram | How It's Calculated |
|----------------------------|----------|-----------|----------|---------------------|
| **Peak QPS (Queries Per Second)** | 1M QPS    | 370K QPS  | 350K QPS  | Total Messages ÷ Seconds per Day |
| **Avg. Response Time**      | <50ms    | <40ms     | <30ms    | Low-latency chat requirements |
| **Database Servers**        | 25K      | 8K        | 10K      | Each DB handles ~40K QPS |
| **Cache Size**              | 5 PB     | 1 PB      | 3 PB     | 10-15% of active data cached |
| **Application Servers**     | 50K      | 25K       | 23K      | Each server handles ~15K-20K QPS |
| **Total Network Bandwidth Required** | 24 Tbps  | 7.4 Tbps  | 9.7 Tbps  | Total Data Sent ÷ Seconds per Day × 8 |

---

# Storage and Database Considerations

| Storage Type     | WhatsApp | Messenger | Telegram | How It's Calculated |
|-----------------|----------|-----------|----------|---------------------|
| **Hot Storage (SSDs, NVMe)**  | 5 PB   | 1 PB     | 3 PB     | Stores frequently accessed messages/media |
| **Warm Storage (HDDs, Object Storage)** | 10 PB  | 3 PB     | 7 PB     | Less frequently accessed messages |
| **Cold Storage (Archived Messages)** | 50 PB  | 10 PB    | 30 PB    | Long-term message retention |

---

# Caching Layer (Redis, Memcached, etc.)

| Cache Type              | WhatsApp | Messenger | Telegram | How It's Calculated |
|-------------------------|----------|-----------|----------|---------------------|
| **Active Chat Cache**   | 2 PB     | 500 TB    | 1.5 PB   | Stores recent messages for fast access |
| **User Presence Cache** | 100 TB   | 50 TB     | 80 TB    | Online/offline status tracking |
| **Cache Expiry (TTL)**  | 5-10 min | 3-5 min   | 10-15 min | Messages expire from cache quickly |

---

# Backend Services and Worker Nodes

| Component                  | WhatsApp | Messenger | Telegram | How It's Calculated |
|----------------------------|----------|-----------|----------|---------------------|
| **Message Routing Workers** | 50K      | 20K       | 30K      | Manages message delivery queues |
| **Real-time Processing Workers** | 20K | 8K        | 12K      | Processes status updates, seen receipts |
| **Machine Learning for Spam Detection** | 10K GPUs | 4K GPUs  | 6K GPUs  | AI filters spam/scam messages |
| **API Gateway & Load Balancers** | 5K | 2K        | 3K       | Balances user request load |

---

# Network & Infrastructure

| Infrastructure             | WhatsApp | Messenger | Telegram | How It's Calculated |
|----------------------------|----------|-----------|----------|---------------------|
| **Total Network Throughput** | 24 Tbps  | 7.4 Tbps  | 9.7 Tbps  | Total Data Sent ÷ 86400 × 8 |
| **Number of Data Centers**   | 15+      | 8         | 10       | Distributed for redundancy |
| **Edge CDN Servers**         | 8K       | 3K        | 5K       | Used for optimizing media transfers |

---

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category        | WhatsApp (Annual) | Messenger | Telegram | How It's Calculated |
|----------------------|------------------|-----------|----------|---------------------|
| **Compute (AWS/GCP/Azure)** | $2.5B   | $700M     | $1.2B    | Cloud instance pricing |
| **Storage Costs**    | $1.8B   | $500M     | $900M    | Object storage (S3, EBS, HDD) |
| **Network Costs**    | $600M   | $150M     | $400M    | Cloud data egress pricing |
| **Total Cost Estimate** | $4.9B   | $1.35B    | $2.5B    | Sum of all above |


