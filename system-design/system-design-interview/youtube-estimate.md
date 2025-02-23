# Assumptions & Inputs

| Metric                        | Estimate             | How It’s Calculated / Assumed |
|-------------------------------|----------------------|------------------------------|
| **Total Users**                | ~2.7 billion         | Public data from YouTube reports |
| **Monthly Active Users (MAU)** | ~2.7 billion         | Nearly all users engage at least once a month |
| **Daily Active Users (DAU)**   | ~1.35 billion (50% of MAU) | Industry-standard DAU/MAU ratio |
| **Avg. Watch Time per DAU**    | ~50 minutes (3000 sec) | YouTube reports, industry analysis |
| **Total Watch Hours/Day**      | ~7 billion hours     | **DAU × Watch Time/User** |
| **Avg. Video Bitrate (1080p)** | ~5 Mbps              | Standard video streaming quality |
| **Total Data Streamed/Day**    | ~15.5 Exabytes (EB)  | **Total Watch Hours × Bitrate** |
| **Avg. Video Length**          | ~12 minutes (~720 sec) | YouTube reports, industry studies |
| **Videos Watched per User/Day**| ~10-15 videos        | Estimated from session engagement data |
| **Total Video Views per Day**  | ~15-20 billion views | **DAU × Videos Watched per User** |
| **Uploads per Minute**         | ~700-800 hours       | YouTube stats |
| **Total Uploads per Day**      | ~1 million hours (~42PB) | **Uploads/Minute × 1440 min** |
| **Peak QPS (Queries Per Second)** | ~1.5 million QPS    | **Total Video Requests/Day ÷ 86,400 sec** |

---

# Compute & Storage Requirements

| Component                       | Estimate             | How It’s Calculated |
|----------------------------------|----------------------|---------------------|
| **Peak Queries Per Second (QPS)** | ~1.5 million QPS     | Estimated from traffic patterns |
| **Avg. Response Time**          | <50ms                | Optimized with caching/CDN |
| **Storage per Video (1080p, H.264)** | ~1.5 GB per hour    | Compression standards |
| **Total Storage for Videos**    | ~50+ Exabytes (EB)   | Cumulative data stored |
| **New Storage Required/Day**    | ~42 PB               | **Total Uploads per Day** |
| **Database Servers**            | ~100,000+            | Each handles ~30-50K QPS |
| **Application Servers**         | ~200,000+            | Each handles ~7,500 requests/sec |
| **Edge Cache Servers (CDN)**    | ~50,000+             | For latency optimization |

---

# Caching & Data Distribution

| Cache Type                     | Size                | How It’s Used |
|---------------------------------|---------------------|---------------|
| **User Watch History Cache**    | ~5 PB               | Stores recent watch data |
| **Video Content Cache (CDN)**   | ~30-40 PB           | Cached at edge locations |
| **Trending Videos Cache**       | ~10 PB              | Frequently watched videos |
| **Cache Expiry (TTL)**          | 1-10 minutes        | Adjusted dynamically |

---

# Network Infrastructure

| Metric                         | Estimate             | How It’s Calculated |
|---------------------------------|----------------------|---------------------|
| **Total Network Throughput**    | ~40+ Tbps            | Data streamed globally |
| **Total Data Served/Day**       | ~15.5 Exabytes (EB)  | Video streaming volume |
| **Edge Data Centers**           | ~30+ globally        | Reducing latency |
| **CDN Servers**                 | ~50,000+             | Cache popular videos |

---

# AI, Machine Learning & Processing

| Component                        | Estimate             | How It’s Used |
|----------------------------------|----------------------|---------------|
| **Recommendation AI GPUs**      | ~30,000 NVIDIA GPUs  | Personalized recommendations |
| **Ad Targeting ML Models**      | ~20,000 GPUs         | Ad relevance & bidding |
| **Content Moderation AI**       | ~15,000 GPUs         | Detect policy violations |
| **Video Transcoding Servers**   | ~50,000+             | Multi-resolution conversion |

---

# Energy & Cost Estimates

| Cost Category                   | Annual Estimate      | How It’s Calculated |
|----------------------------------|----------------------|---------------------|
| **Compute (AWS/GCP Equivalent)** | $5-7 billion         | Based on cloud pricing |
| **Storage Costs**               | $3 billion           | S3/EBS equivalents |
| **Network Costs**               | $2 billion           | Bandwidth pricing |
| **Total Annual Cost**           | $10-12 billion       | Sum of all above |





