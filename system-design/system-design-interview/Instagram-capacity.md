## 1. Assumptions & Inputs

| Metric | Instagram (Estimated 2024) | How It’s Calculated |
|--------|---------------------------|---------------------|
| **Total Users** | 2.5B+ | Reported by Meta |
| **Daily Active Users (DAU)** | 700M+ | `DAU ≈ 28% × 2.5B` |
| **Monthly Active Users (MAU)** | 2.2B+ | `MAU ≈ 85% × 2.5B` |
| **Peak Concurrent Users** | 100M+ | `15% × 700M` |
| **Photos Uploaded Per Day** | 1B+ | `1.5 photos/user × 700M` |
| **Videos Uploaded Per Day** | 300M+ | `0.4 videos/user × 700M` |
| **Stories Shared Per Day** | 500M+ | Instagram reports this directly |
| **Reels Uploaded Per Day** | 200M+ | `0.3 reels/user × 700M` |
| **Messages Sent Per Day** | 2B+ | Estimated user engagement |
| **Avg. Image Size** | 3 MB | Compressed JPEG (~1080p resolution) |
| **Avg. Video Size** | 50 MB | Compressed MP4 (1080p, 60s) |
| **Avg. Reels Size** | 100 MB | Longer-form video, higher resolution |
| **Peak Queries Per Second (QPS)** | 2M+ | Estimated from API calls |

---

## 2. Compute and Memory Requirements

| Component | Compute Instances | Memory Per Node | How It’s Calculated |
|-----------|------------------|----------------|---------------------|
| **App Servers (API + Web Frontend)** | 80,000+ | 128GB+ RAM | `2M QPS × 100ms ÷ 15K QPS/server` |
| **Image Processing Servers** | 50,000+ GPUs | 256GB RAM | `1B images/day × 3MB ÷ 100K images/server` |
| **Video Encoding Servers** | 80,000+ GPUs | 512GB RAM | `300M × 50MB ÷ 50K videos/server` |
| **Reels Processing Servers** | 30,000+ GPUs | 512GB RAM | `200M × 100MB ÷ 50K reels/server` |
| **Machine Learning (Feed Ranking, Ads, Spam Detection)** | 120,000+ GPUs | 1TB RAM | Large-scale AI model training and inference |
| **Messaging & Notifications Servers** | 25,000+ | 128GB RAM | `2B messages/day ÷ 1M messages/server` |

---

## 3. Storage and Database Considerations

| Storage Type | Total Storage | How It’s Calculated |
|-------------|--------------|---------------------|
| **Total Data Stored** | 1.5+ Exabytes | All user-generated content (images, videos, messages, metadata) |
| **Daily Data Growth** | 3+ Petabytes | `(1B × 3MB) + (300M × 50MB) + (200M × 100MB)` |
| **Hot Storage (SSD, NVMe)** | 300 PB | `20% × 1.5EB` |
| **Cold Storage (HDD, Tape)** | 1.2 EB | `80% × 1.5EB` |
| **Object Store (Photos & Videos)** | 900 PB+ | Data distributed across storage clusters |

### Database Details (PostgreSQL, Cassandra, RocksDB, MySQL)

| Database | Estimated Nodes | How It’s Calculated |
|----------|---------------|---------------------|
| **User Profiles & Followers** | 50,000+ Nodes | `2.5B users × 5KB ÷ 50GB/node` |
| **Posts & Comments** | 80,000+ Nodes | `1B × 5KB ÷ 50GB/node` |
| **Likes & Engagement** | 60,000+ Nodes | `10B × 1KB ÷ 50GB/node` |
| **Messaging (DMs, Threads)** | 40,000+ Nodes | `2B × 10KB ÷ 50GB/node` |

---

## 4. Caching Layer (Memcached, Redis, CDN)

| Cache Type | Cache Size | How It’s Calculated |
|------------|-----------|---------------------|
| **User Session Cache** | 50 PB+ | `700M DAU × 100KB` |
| **Feed Generation Cache** | 80 PB+ | `700M × 10KB per user` |
| **Reels & Stories Cache** | 40 PB+ | `500M × 80MB per user` |
| **DM Messages Cache** | 30 PB+ | `2B × 10KB` |
| **Global CDN Cache** | 200+ PB | `Total Served Data ÷ Avg. Cache TTL` |

---

## 5. Network & Infrastructure

| Infrastructure | Estimated Capacity | How It’s Calculated |
|---------------|--------------------|---------------------|
| **Total Network Throughput** | 20+ Tbps | `(3PB × 8) ÷ 86,400s` |
| **Number of Data Centers** | 60+ | Distributed globally |
| **Edge Security Servers** | 50,000+ | For DDoS protection |

---

## 6. Cost Estimates (Cloud Equivalent Pricing)

| Cost Category | Estimated Annual Cost | How It’s Calculated |
|--------------|----------------------|---------------------|
| **Compute (AWS/GCP/Azure Equivalent)** | $15B+ | `80K app servers × $100K` |
| **Storage Costs** | $8B+ | `1.5EB ÷ 1PB × $5M per PB` |
| **CDN & Network Costs** | $6B+ | `20Tbps × $250K` |
| **Total Cost Estimate** | $30B+ | Sum of all above |
