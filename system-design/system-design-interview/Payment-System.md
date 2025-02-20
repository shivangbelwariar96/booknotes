# Assumptions & Inputs

| Metric                                   | Visa    | Mastercard | PayPal   | Stripe   | How It’s Calculated                             |
|------------------------------------------|---------|------------|----------|----------|-------------------------------------------------|
| Total Transactions Per Day               | 600M+   | 500M+      | 300M+    | 100M+    | Public transaction volume data                  |
| Peak Transactions Per Second (TPS)       | 65K+    | 50K+       | 10K+     | 5K+      | TPS varies based on peak shopping times         |
| Avg. Transaction Size                    | 1 KB    | 1 KB       | 1 KB     | 1 KB     | Includes metadata, amount, user details         |
| New Users Per Day                        | 500K+   | 400K+      | 300K+    | 200K+    | Estimated growth rate                           |
| Fraud Detection Requests/Day             | 2B+     | 1.5B+      | 1B+      | 500M+    | AI-based fraud detection is run on transactions |
| Total Data Processed Per Day             | 600 TB+ | 500 TB+    | 300 TB+  | 100 TB+  | Transactions × Avg. Size                        |

# Compute and Memory Requirements

| Component                                | Visa    | Mastercard | PayPal   | Stripe   | How It’s Calculated                             |
|------------------------------------------|---------|------------|----------|----------|-------------------------------------------------|
| Peak TPS (Transactions Per Second)      | 65K+    | 50K+       | 10K+     | 5K+      | Transactions per day ÷ 86,400 sec              |
| Avg. API Latency                         | <50ms   | <80ms      | <100ms   | <120ms   | Speed of transaction processing                |
| Compute Nodes (App Servers)              | 50K+    | 40K+       | 20K+     | 10K+     | Handles payment requests and routing            |
| Fraud Detection Servers                  | 100K+   | 80K+       | 50K+     | 20K+     | AI/ML models to detect fraud                    |
| Cache Servers (Redis, Memcached)         | 20K+    | 15K+       | 10K+     | 5K+      | Caching session/user data                       |
| Total Network Bandwidth                  | 5+ Tbps | 3+ Tbps    | 2+ Tbps  | 1+ Tbps  | Handling secure payment transactions            |

# Storage and Database Considerations

| Storage Type                             | Visa    | Mastercard | PayPal   | Stripe   | How It’s Calculated                             |
|------------------------------------------|---------|------------|----------|----------|-------------------------------------------------|
| Total Storage                            | 100 PB+ | 80 PB+     | 50 PB+   | 20 PB+   | Transaction logs, user data                    |
| Hot Storage (SSDs, NVMe)                 | 10 PB   | 8 PB       | 5 PB     | 2 PB     | For real-time transaction access               |
| Warm Storage (HDDs, Object Storage)      | 50 PB   | 40 PB      | 25 PB    | 10 PB    | Transaction history, analytics                 |
| Cold Storage (Backup, Compliance)        | 100 PB  | 80 PB      | 50 PB    | 20 PB    | Archived transaction data                       |
| Data Replication Factor                  | 3x - 6x | 3x         | 3x       | 3x       | Ensuring redundancy and durability              |

# Caching Layer (Metadata, Transaction Status, Fraud Data)

| Cache Type                               | Visa    | Mastercard | PayPal   | Stripe   | How It’s Calculated                             |
|------------------------------------------|---------|------------|----------|----------|-------------------------------------------------|
| Session Cache                            | 5 PB    | 4 PB       | 3 PB     | 1 PB     | Caching user session data                       |
| Transaction Response Cache               | 1 PB    | 500 TB     | 300 TB   | 100 TB   | Caching frequent queries                        |
| Fraud Detection Cache                    | 10 PB   | 8 PB       | 5 PB     | 2 PB     | Stores recent fraud patterns                    |
| Cache Expiry (TTL)                       | 1-5 min | 5-10 min   | 10-15 min| 10-20 min| Based on fraud prevention needs                 |

# Backend Services and Worker Nodes

| Component                                | Visa    | Mastercard | PayPal   | Stripe   | How It’s Calculated                             |
|------------------------------------------|---------|------------|----------|----------|-------------------------------------------------|
| Payment Routing Workers                  | 50K+    | 40K+       | 20K+     | 10K+     | Handles transaction routing                     |
| Fraud Detection AI Models                | 100K GPUs| 80K GPUs   | 50K GPUs | 20K GPUs  | AI-powered fraud detection                      |
| API Gateway & Load Balancers             | 10K     | 7K         | 5K       | 3K       | Handles API requests distribution               |

# Network & Infrastructure

| Infrastructure                            | Visa    | Mastercard | PayPal   | Stripe   | How It’s Calculated                             |
|-------------------------------------------|---------|------------|----------|----------|-------------------------------------------------|
| Total Network Throughput                  | 5 Tbps  | 3 Tbps     | 2 Tbps   | 1 Tbps   | Data ingress + egress traffic                  |
| Number of Data Centers                    | 50+     | 30+        | 20+      | 15+      | Spread across geographies                       |
| Edge Security Servers                     | 10K+    | 7K+        | 5K+      | 3K+      | Protecting against cyber threats                |

# Cost Estimates (Cloud Equivalent Pricing)

| Cost Category                             | Visa (Annual) | Mastercard | PayPal   | Stripe   | How It’s Calculated                             |
|-------------------------------------------|---------------|------------|----------|----------|-------------------------------------------------|
| Compute (AWS/GCP/Azure)                   | $5B           | $4B        | $2B      | $1B      | Based on cloud instance costs                  |
| Storage Costs                             | $3B           | $2.5B      | $1.5B    | $750M    | Based on compliance & backup needs             |
| Network Costs                             | $2B           | $1.5B      | $1B      | $500M    | Based on secure transaction routing            |
| Total Cost Estimate                       | $10B+         | $8B+       | $4.5B+   | $2.25B+   | Sum of all above                               |
