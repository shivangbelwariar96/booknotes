# System Design Documentation

## Chapter 5 - Design A Rate Limiter

A rate limiter controls the number of requests a user or service can make within a given time period.

### Services

| Service Name        | Description                                      |
|---------------------|--------------------------------------------------|
| checkRateLimit     | Checks if a request should be allowed based on rate limits. |
| updateRateLimitRule | Allows admin to update rate limit rules.        |
| getRateLimitRule   | Allows admin to retrieve specific rate limit rules. |

### API Details & Schemas

| Service Name         | Req Type | API URL                        | Request JSON Schema                                        | Response JSON Schema                          |
|----------------------|---------|--------------------------------|----------------------------------------------------------|---------------------------------------------|
| checkRateLimit      | POST    | `/api/v1/rate_limit/check`    | `{ "user_id": "string", "api_endpoint": "string" }` | `{ "allowed": boolean, "retry_after_seconds": integer }` |
| updateRateLimitRule | PUT     | `/api/v1/rate_limit/rule`     | `{ "rule_id": "string", "limit": integer, "time_window": integer, "target": "string" }` | `{ "rule_id": "string", "status": "string" }` |
| getRateLimitRule    | GET     | `/api/v1/rate_limit/rule/{rule_id}` | `{}` | `{ "rule_id": "string", "limit": integer, "time_window": integer, "target": "string" }` |

### Database Design

#### MySQL Tables

| Table Name       | Column Name   | Data Type        | Primary Key | Foreign Key | Indexes           |
|-----------------|--------------|----------------|-------------|-------------|------------------|
| RateLimitRules  | rule_id       | VARCHAR(255)   | YES         |             |                  |
|                 | limit         | INT            | NO          |             |                  |
|                 | time_window   | INT            | NO          |             |                  |
|                 | target        | VARCHAR(255)   | NO          |             | Index on target  |
|                 | created_at    | TIMESTAMP      | NO          |             |                  |
|                 | updated_at    | TIMESTAMP      | NO          |             |                  |

#### Redis Data Structure (for Counters)

- **Key:** `rate_limit:{rule_id}:{target_identifier}` (e.g., `rate_limit:rule_123:user_456` or `rate_limit:rule_123:/api/resource`)
- **Value:** Current request count within the time window (integer) & Timestamp of last reset/update.

---







## Chapter 8 - Design A Unique ID Generator In Distributed Systems

A Unique ID Generator ensures globally unique IDs across multiple servers and datacenters.

### Services

| Service Name       | Description                                      |
|--------------------|--------------------------------------------------|
| generateUniqueID  | Generates a new unique ID.                      |
| configureGenerator | Allows admin to configure the ID generator.    |
| getIDMetadata     | Retrieves metadata about a generated ID.        |

### API Details & Schemas

| Service Name         | Req Type | API URL                         | Request JSON Schema                                    | Response JSON Schema                                  |
|----------------------|---------|---------------------------------|------------------------------------------------------|----------------------------------------------------|
| generateUniqueID    | GET     | `/api/v1/unique_id/generate`   | `{}`                                                 | `{ "unique_id": "string" }`                     |
| configureGenerator  | PUT     | `/api/v1/unique_id/config`     | `{ "datacenter_id": integer, "worker_id": integer }` | `{ "status": "string" }`                         |
| getIDMetadata       | GET     | `/api/v1/unique_id/metadata/{unique_id}` | `{}` | `{ "timestamp": integer, "datacenter_id": integer, "worker_id": integer }` |

### Database Design

#### MySQL Tables (Optional Configuration Storage)

| Table Name         | Column Name    | Data Type       | Primary Key | Foreign Key | Indexes |
|--------------------|---------------|---------------|-------------|-------------|---------|
| IDGeneratorConfig | config_key     | VARCHAR(255)  | YES         |             |         |
|                   | config_value   | TEXT          | NO          |             |         |
|                   | last_updated_at| TIMESTAMP     | NO          |             |         |

#### Core ID Generation Logic

- **Algorithm-based (e.g., Snowflake)**
- **Structure of ID:** `Timestamp (milliseconds) - Datacenter ID - Worker ID - Sequence Number`
- **No Database for each ID generation**, relies on in-memory counters and system clock.

---





# Chapter 9 - Design A URL Shortener

A URL Shortener converts long URLs into shorter, more manageable URLs.

## Services

| Service Name       | Description                                        |
|--------------------|----------------------------------------------------|
| createShortURL    | Generates a short URL for a given long URL.        |
| resolveShortURL   | Redirects from a short URL to the original long URL. |
| getURLStats       | Retrieves statistics for a short URL (e.g., clicks). |
| updateCustomAlias | Allows user to update a custom alias for a short URL. |

## API Details & Schemas

| Service Name       | Req Type | API URL                             | Request JSON Schema                                         | Response JSON Schema                                      |
|--------------------|---------|-------------------------------------|--------------------------------------------------------------|------------------------------------------------------------|
| createShortURL    | POST    | /api/v1/url/shorten                | `{ "long_url": "string", "custom_alias": "string (optional)" }` | `{ "short_url": "string" }`                             |
| resolveShortURL   | GET     | /{short_url_code}                   | `{}`                                                         | `(302 Redirect to long URL)`                             |
| getURLStats       | GET     | /api/v1/url/stats/{short_url_code} | `{}`                                                         | `{ "short_url_code": "string", "long_url": "string", "clicks": integer, "created_at": timestamp }` |
| updateCustomAlias | PUT     | /api/v1/url/alias/{short_url_code}  | `{ "custom_alias": "string" }`                              | `{ "short_url_code": "string", "status": "string" }` |

## Database: MySQL (for persistence and relationships)

### MySQL Tables

#### `ShortenedURLs`

| Column Name    | Data Type      | Primary Key | Foreign Key | Indexes                           |
|---------------|---------------|-------------|-------------|-----------------------------------|
| id            | INT AUTO_INCREMENT | YES         |             |                                   |
| short_url_code | VARCHAR(255)   | NO          |             | UNIQUE Index on short_url_code    |
| long_url      | TEXT           | NO          |             | Index on long_url (for lookups)   |
| custom_alias  | VARCHAR(255)   | NO          |             | UNIQUE Index on custom_alias      |
| created_at    | TIMESTAMP      | NO          |             |                                   |
| updated_at    | TIMESTAMP      | NO          |             |                                   |

#### `URLStats`

| Column Name      | Data Type  | Primary Key | Foreign Key | Indexes                     |
|-----------------|-----------|-------------|-------------|-----------------------------|
| id             | INT AUTO_INCREMENT | YES         |             |                             |
| short_url_id   | INT       | NO          | YES         | Index on short_url_id       |
| click_timestamp | TIMESTAMP | NO          |             | Index on click_timestamp    |

Foreign Key:
```
FOREIGN KEY (short_url_id) REFERENCES ShortenedURLs(id)
```

---








# Chapter 10 - Design A Web Crawler

A Web Crawler systematically browses the World Wide Web, typically for the purpose of Web indexing.

## Services

| Service Name       | Description                                      |
|--------------------|--------------------------------------------------|
| startCrawl        | Initiates a new web crawl from a seed URL.       |
| getCrawlStatus    | Retrieves the status of a running or completed crawl. |
| addSeedURL        | Adds a new seed URL to the crawler.              |
| getDiscoveredURLs | Retrieves a list of URLs discovered by the crawler. |

## API Details & Schemas

| Service Name       | Req Type | API URL                            | Request JSON Schema                                  | Response JSON Schema                                                                 |
|--------------------|---------|------------------------------------|-----------------------------------------------------|-------------------------------------------------------------------------------------|
| startCrawl        | POST    | /api/v1/crawler/start              | `{ "seed_url": "string" }`                        | `{ "crawl_id": "string", "status": "string" }`                           |
| getCrawlStatus    | GET     | /api/v1/crawler/status/{crawl_id}  | `{}`                                               | `{ "crawl_id": "string", "status": "string", "urls_crawled": integer, "urls_pending": integer, "start_time": timestamp, "end_time": timestamp }` |
| addSeedURL        | POST    | /api/v1/crawler/seed_url           | `{ "seed_url": "string" }`                        | `{ "status": "string" }`                                                     |
| getDiscoveredURLs | GET     | /api/v1/crawler/urls/{crawl_id}    | `{ "page": integer, "per_page": integer }`        | `{ "crawl_id": "string", "urls": ["string"], "total_urls": integer, "page": integer, "total_pages": integer }` |

## Database: NoSQL (e.g., MongoDB or Cassandra)

NoSQL is beneficial for handling large volumes of URLs, crawled content, and flexible schema.

### NoSQL Database (Example: MongoDB - Collections)

#### Collection: `CrawlTasks`

```json
{
  "_id": "crawl_id_123",
  "seed_url": "string",
  "status": "pending" | "running" | "completed" | "failed",
  "start_time": timestamp,
  "end_time": timestamp,
  "urls_crawled_count": integer,
  "urls_pending_count": integer
}
```

#### Collection: `CrawledPages`

```json
{
  "_id": "unique_url_hash", // or use URL itself as _id, careful with URL length limits
  "url": "string",
  "crawl_id": "crawl_id_123", // Reference to CrawlTasks
  "content": "HTML content string",
  "status_code": integer,
  "discovered_links": ["string"],
  "crawl_timestamp": timestamp
}
```

#### Collection: `URLQueue` (For managing URLs to be crawled, can use Redis also for queue)

```json
{
  "url": "string",
  "crawl_id": "crawl_id_123", // Reference to CrawlTasks
  "priority": integer,
  "added_timestamp": timestamp
}










# Chapter 11 - Design A Notification System

A Notification System delivers real-time notifications to users across various channels.

## Services

| Service Name              | Description                                               |
|---------------------------|-----------------------------------------------------------|
| sendNotification          | Sends a notification to a user or a group of users.      |
| getUserNotifications      | Retrieves a user's notifications.                        |
| markNotificationAsRead    | Marks a notification as read.                            |
| createUserSubscription    | Allows user to subscribe to notification types/topics.   |
| getUserSubscriptions      | Retrieves a user's notification subscriptions.           |

## API Details & Schemas

| Service Name             | Req Type | API URL                                      | Request JSON Schema                                                                 | Response JSON Schema                                   |
|--------------------------|----------|----------------------------------------------|------------------------------------------------------------------------------------|------------------------------------------------------|
| sendNotification        | POST     | /api/v1/notifications/send                   | `{ "user_ids": ["string"], "notification_type": "string", "message": "string", "data": {} }` | `{ "notification_ids": ["string"], "status": "string" }` |
| getUserNotifications    | GET      | /api/v1/notifications/user/{user_id}        | `{ "page": integer, "per_page": integer }`                                       | `{ "notifications": [{}], "total_notifications": integer, "page": integer, "total_pages": integer }` |
| markNotificationAsRead  | PUT      | /api/v1/notifications/read/{notification_id} | `{}`                                                                               | `{ "notification_id": "string", "status": "string" }` |
| createUserSubscription  | POST     | /api/v1/notifications/subscribe              | `{ "user_id": "string", "notification_type": "string", "channel_preferences": ["push", "email", "sms"] }` | `{ "subscription_id": "string", "status": "string" }` |
| getUserSubscriptions    | GET      | /api/v1/notifications/subscriptions/{user_id} | `{}`                                                                              | `{ "subscriptions": [{}] }` |

## Database: NoSQL (e.g., Cassandra or MongoDB)

### NoSQL Database (Example: Cassandra - Tables/Column Families)

#### Table: Notifications
| Column Name         | Data Type        |
|---------------------|-----------------|
| notification_id     | UUID (Primary Key) |
| user_id            | List or Set of User IDs |
| notification_type  | Text |
| message           | Text |
| data              | Map<Text, Text> |
| created_at        | Timestamp |
| status           | Text ("sent", "pending", "failed") |

#### Table: UserNotifications
| Column Name         | Data Type |
|---------------------|----------|
| user_id            | UUID (Partition Key) |
| notification_id    | UUID (Clustering Key - for ordering) |
| notification_type  | Text |
| message           | Text |
| created_at        | Timestamp |
| is_read          | Boolean |

#### Table: UserSubscriptions
| Column Name           | Data Type |
|-----------------------|----------|
| subscription_id       | UUID (Primary Key) |
| user_id              | UUID (Index) |
| notification_type    | Text |
| channel_preferences  | Set<Text> (e.g., "push", "email", "sms") |
| created_at          | Timestamp |

---









# Chapter 12 - Design A News Feed System

A News Feed System displays relevant and personalized content to users, typically in a chronological or ranked order.

## Services

| Service Name   | Description                                  |
|---------------|----------------------------------------------|
| publishPost   | Allows users to publish new posts.          |
| getNewsFeed   | Retrieves the news feed for a user.         |
| followUser    | Allows a user to follow another user.       |
| unfollowUser  | Allows a user to unfollow another user.     |
| getUserProfile | Retrieves a user's profile information.    |
| likePost      | Allows a user to like a post.               |
| unlikePost    | Allows a user to unlike a post.             |

## API Details & Schemas

| Service Name  | Req Type | API URL                        | Request JSON Schema                                        | Response JSON Schema                                     |
|--------------|----------|--------------------------------|-----------------------------------------------------------|----------------------------------------------------------|
| publishPost  | POST     | /api/v1/newsfeed/post         | `{ "user_id": "string", "content": "string", "media_urls": ["string"] }` | `{ "post_id": "string", "status": "string" }` |
| getNewsFeed  | GET      | /api/v1/newsfeed/{user_id}    | `{ "page": integer, "per_page": integer }`               | `{ "feed_items": [{}], "total_items": integer, "page": integer, "total_pages": integer }` |

## Database: Mix of NoSQL and Relational (MySQL)

### NoSQL Database (Cassandra - for Posts and Feed Aggregation)

#### Table: Posts
| Column Name  | Data Type |
|-------------|----------|
| post_id     | UUID (Primary Key) |
| user_id     | UUID (Index) |
| content     | Text |
| media_urls  | List<Text> |
| created_at  | Timestamp |
| updated_at  | Timestamp |

#### Table: UserFollows
| Column Name      | Data Type |
|-----------------|----------|
| follower_user_id | UUID (Partition Key) |
| followee_user_id | UUID (Clustering Key) |
| created_at       | Timestamp |

---









# Chapter 13 - Design A Chat System

A Chat System enables real-time text-based communication between users.

## Services

| Service Name        | Description                                      |
|---------------------|--------------------------------------------------|
| sendMessage        | Sends a chat message to a user or chat group.    |
| getChatMessages    | Retrieves messages for a specific chat or user conversation. |
| createChatGroup    | Creates a new chat group with multiple users.    |
| addUserToGroup     | Adds a user to an existing chat group.           |
| removeUserFromGroup | Removes a user from a chat group.               |
| getChatGroupInfo   | Retrieves information about a chat group.        |
| getUserChats       | Retrieves a list of chats a user is part of.     |
| markMessageAsRead  | Marks a message as read by a user.               |

## API Details & Schemas

| Service Name       | Req Type | API URL                                | Request JSON Schema | Response JSON Schema |
|--------------------|---------|----------------------------------------|---------------------|----------------------|
| sendMessage       | POST    | `/api/v1/chat/message/send`           | `{ "sender_user_id": "string", "receiver_id": "string (user_id or group_id)", "message_type": "text | media", "content": "string", "media_url": "string (optional)" }` | - |
| getChatMessages   | GET     | `/api/v1/chat/messages/{chat_id}`      | `{ "chat_type": "user | group", "participant_user_id": "string", "page": integer, "per_page": integer }` | - |
| createChatGroup   | POST    | `/api/v1/chat/group/create`           | `{ "group_name": "string", "creator_user_id": "string", "member_user_ids": ["string"] }` | `{ "group_id": "string", "status": "string" }` |
| addUserToGroup    | POST    | `/api/v1/chat/group/add_user`         | `{ "group_id": "string", "user_id": "string" }` | `{ "status": "string" }` |
| removeUserFromGroup | POST  | `/api/v1/chat/group/remove_user`       | `{ "group_id": "string", "user_id": "string" }` | `{ "status": "string" }` |
| getChatGroupInfo  | GET     | `/api/v1/chat/group/{group_id}`        | `{}` | `{ "group_id": "string", "group_name": "string", "member_user_ids": ["string"], "created_at": timestamp, "creator_user_id": "string" }` |
| getUserChats      | GET     | `/api/v1/chat/user/{user_id}`          | `{ "page": integer, "per_page": integer }` | `{ "chats": [{}], "total_chats": integer, "page": integer, "total_pages": integer }` |
| markMessageAsRead | PUT     | `/api/v1/chat/message/read/{message_id}` | `{ "user_id": "string" }` | `{ "message_id": "string", "status": "string" }` |

## Database: NoSQL (MongoDB or Cassandra)

### Messages Collection

```json
{
  "_id": "message_id_123",
  "sender_user_id": "string",
  "receiver_id": "string (user_id or group_id)",
  "chat_type": "user" | "group",
  "message_type": "text" | "media",
  "content": "string",
  "media_url": "string (optional)",
  "timestamp": "timestamp",
  "is_read_by": ["user_id_1", "user_id_2"]
}
```

### ChatGroups Collection

```json
{
  "_id": "group_id_123",
  "group_name": "string",
  "creator_user_id": "string",
  "member_user_ids": ["string"],
  "created_at": "timestamp"
}
```

### UserChats Collection

```json
{
  "user_id": "string",
  "chat_ids": ["chat_id_1", "chat_id_2"]
}
```

---





# Chapter 14 - Design A Search Autocomplete System

A Search Autocomplete System provides suggestions as a user types a search query.

## Services

| Service Name       | Description                                      |
|--------------------|--------------------------------------------------|
| getSuggestions    | Retrieves autocomplete suggestions for a given prefix. |
| reportSearchQuery | Reports a user search query to improve suggestions. |
| addSuggestion     | Allows admin to manually add a suggestion.       |
| removeSuggestion  | Allows admin to remove a suggestion.             |

## API Details & Schemas

| Service Name       | Req Type | API URL                                | Request JSON Schema | Response JSON Schema |
|--------------------|---------|----------------------------------------|---------------------|----------------------|
| getSuggestions    | GET     | `/api/v1/autocomplete/suggestions`    | `{ "prefix": "string", "limit": integer }` | `{ "prefix": "string", "suggestions": ["string"] }` |
| reportSearchQuery | POST    | `/api/v1/autocomplete/report`         | `{ "query": "string" }` | `{ "status": "string" }` |
| addSuggestion     | POST    | `/api/v1/autocomplete/suggestion`     | `{ "suggestion": "string" }` | `{ "status": "string" }` |
| removeSuggestion  | DELETE  | `/api/v1/autocomplete/suggestion`     | `{ "suggestion": "string" }` | `{ "status": "string" }` |

## Database: Redis (Trie/Sorted Sets) or Elasticsearch

### Redis Data Structures

#### Trie (Prefix Tree) in Redis
- Store suggestions in a Trie structure where each node represents a character.
- At leaf nodes, store a reference to the full suggestion and potentially a popularity score.

#### Sorted Sets for Ranking
- Use Sorted Sets to store suggestions with scores based on popularity.
- **Key:** `autocomplete:suggestions:{prefix}`
- **Members:** `suggestion_string`
- **Scores:** `popularity_score`

## MySQL Tables (Optional - Persistent Storage for Suggestions)

| Table Name                | Column Name     | Data Type         | Primary Key | Foreign Key | Indexes                  |
|---------------------------|----------------|-------------------|-------------|-------------|--------------------------|
| AutocompleteSuggestions  | suggestion_id  | INT AUTO_INCREMENT | YES         | -           | -                        |
|                           | suggestion_text | VARCHAR(255)     | NO          | -           | UNIQUE Index on suggestion_text |
|                           | popularity      | INT              | NO          | -           | Index on popularity       |
|                           | created_at      | TIMESTAMP        | NO          | -           | -                        |
|                           | updated_at      | TIMESTAMP        | NO          | -           | -                        |






# Chapter 15 - Design YouTube

## Design a Video Sharing Platform like YouTube

### Services

| Service Name          | Description                                      |
|----------------------|--------------------------------------------------|
| `uploadVideo`       | Allows users to upload videos.                   |
| `watchVideo`        | Streams a video to a user.                       |
| `searchVideos`      | Searches for videos based on keywords.           |
| `getHomePageFeed`   | Retrieves a personalized homepage feed for a user. |
| `subscribeChannel`  | Allows a user to subscribe to a channel.         |
| `unsubscribeChannel`| Allows a user to unsubscribe from a channel.     |
| `likeVideo`        | Allows a user to like a video.                   |
| `dislikeVideo`      | Allows a user to dislike a video.                |
| `addComment`       | Allows users to add comments to videos.          |
| `getComments`      | Retrieves comments for a video.                  |
| `getVideoMetadata`  | Retrieves metadata about a video.                |
| `getVideoStats`     | Retrieves statistics for a video (views, likes, etc.). |

---

## API Details & Schemas

| Service Name        | Req Type | API URL                                    | Request JSON Schema | Response JSON Schema |
|--------------------|---------|------------------------------------------|---------------------|---------------------|
| `uploadVideo`      | POST    | `/api/v1/youtube/video/upload`         | `{ "user_id": "string", "title": "string", "description": "string", "video_file": "file", "thumbnail_file": "file (optional)" }` | `{ "video_id": "string", "status": "string", "upload_url": "string" }` |
| `watchVideo`       | GET     | `/api/v1/youtube/video/{video_id}`     | `{}` | (Stream video content, JSON metadata) |
| `searchVideos`     | GET     | `/api/v1/youtube/video/search`         | `{ "query": "string", "page": integer, "per_page": integer }` | `{ "videos": [{}], "total_videos": integer, "page": integer, "total_pages": integer }` |
| `getHomePageFeed`  | GET     | `/api/v1/youtube/feed/{user_id}`       | `{ "page": integer, "per_page": integer }` | `{ "feed_items": [{}], "total_items": integer, "page": integer, "total_pages": integer }` |
| `subscribeChannel` | POST    | `/api/v1/youtube/channel/subscribe`    | `{ "subscriber_user_id": "string", "channel_user_id": "string" }` | `{ "status": "string" }` |
| `unsubscribeChannel` | POST  | `/api/v1/youtube/channel/unsubscribe`  | `{ "subscriber_user_id": "string", "channel_user_id": "string" }` | `{ "status": "string" }` |
| `likeVideo`        | POST    | `/api/v1/youtube/video/like`           | `{ "user_id": "string", "video_id": "string" }` | `{ "status": "string", "likes_count": integer }` |
| `dislikeVideo`     | POST    | `/api/v1/youtube/video/dislike`        | `{ "user_id": "string", "video_id": "string" }` | `{ "status": "string", "dislikes_count": integer }` |
| `addComment`       | POST    | `/api/v1/youtube/video/comment`        | `{ "user_id": "string", "video_id": "string", "comment_text": "string" }` | `{ "comment_id": "string", "status": "string", "timestamp": timestamp }` |
| `getComments`      | GET     | `/api/v1/youtube/video/comments/{video_id}` | `{ "page": integer, "per_page": integer }` | `{ "comments": [{}], "total_comments": integer, "page": integer, "total_pages": integer }` |
| `getVideoMetadata` | GET     | `/api/v1/youtube/video/metadata/{video_id}` | `{}` | `{ "video_id": "string", "title": "string", "description": "string", "thumbnail_url": "string", "upload_user_id": "string", "upload_timestamp": timestamp, "duration": integer }` |
| `getVideoStats`    | GET     | `/api/v1/youtube/video/stats/{video_id}` | `{}` | `{ "video_id": "string", "view_count": integer, "likes_count": integer, "dislikes_count": integer, "comment_count": integer }` |

---

## Database Design

### NoSQL Database (MongoDB)

#### Collection: Videos
```json
{
  "_id": "video_id_123",
  "upload_user_id": "string",
  "title": "string",
  "description": "string",
  "video_url": "string",
  "thumbnail_url": "string",
  "duration_seconds": integer,
  "upload_timestamp": timestamp,
  "tags": ["string"]
}
```

#### Collection: VideoComments
```json
{
  "_id": "comment_id_123",
  "video_id": "video_id_123",
  "user_id": "string",
  "comment_text": "string",
  "timestamp": timestamp
}
```

#### Collection: UserVideoActivity (Likes, Dislikes, Views)
```json
{
  "activity_id": "activity_id_123",
  "video_id": "video_id_123",
  "user_id": "string",
  "activity_type": "like" | "dislike" | "view",
  "timestamp": timestamp
}
```

---

### MySQL Tables

#### Users

| Column Name           | Data Type        | Primary Key | Foreign Key | Indexes |
|----------------------|-----------------|-------------|-------------|---------|
| `user_id`           | VARCHAR(255)    | YES         |             |         |
| `username`          | VARCHAR(255)    | NO          |             | UNIQUE  |
| `channel_name`      | VARCHAR(255)    | NO          |             | UNIQUE  |
| `profile_description` | TEXT            | NO          |             |         |

#### VideoMetadata

| Column Name   | Data Type       | Primary Key | Foreign Key |
|--------------|----------------|-------------|-------------|
| `video_id`   | VARCHAR(255)    | YES         | FOREIGN KEY (video_id) REFERENCES Videos(_id) |
| `view_count`  | INT UNSIGNED   | NO          |             |
| `likes_count` | INT UNSIGNED   | NO          |             |
| `dislikes_count` | INT UNSIGNED | NO         |             |
| `comment_count`  | INT UNSIGNED | NO         |             |

#### ChannelSubscriptions

| Column Name         | Data Type        | Primary Key | Foreign Key | Indexes |
|--------------------|-----------------|-------------|-------------|---------|
| `subscription_id` | INT AUTO_INCREMENT | YES       |             |         |
| `subscriber_user_id` | VARCHAR(255)    | NO        | YES         | Index on subscriber_user_id |
| `channel_user_id`  | VARCHAR(255)    | NO        | YES         | Index on channel_user_id |
| `created_at`      | TIMESTAMP       | NO        |             |         |

FOREIGN KEY (subscriber_user_id) REFERENCES Users(user_id)  
FOREIGN KEY (channel_user_id) REFERENCES Users(user_id)









# Chapter 16 - Design Google Drive

## Design a cloud file storage and synchronization service like Google Drive.

### Services

| Service Name         | Description                                            |
|----------------------|--------------------------------------------------------|
| uploadFile          | Allows users to upload files.                          |
| downloadFile        | Allows users to download files.                        |
| createFolder        | Creates new folders for file organization.             |
| listFiles           | Lists files and folders within a directory.            |
| moveFile            | Moves files or folders to a different location.        |
| renameFile          | Renames files or folders.                              |
| deleteFile          | Deletes files or folders.                              |
| shareFile           | Allows users to share files and folders with others.   |
| getSharedLink       | Generates a shareable link for a file or folder.       |
| getFileVersionHistory | Retrieves version history of a file.                 |
| restoreFileVersion  | Restores a file to a previous version.                 |
| searchFiles         | Searches for files based on name or content.           |

### API Details & Schemas

| Service Name             | Req Type | API URL                                   | Request JSON Schema | Response JSON Schema |
|--------------------------|----------|-------------------------------------------|----------------------|----------------------|
| uploadFile              | POST     | /api/v1/drive/file/upload                | `{ "user_id": "string", "parent_folder_id": "string (optional, root if null)", "file_name": "string", "file_content": "file (multipart/form-data)" }` | `{ "file_id": "string", "status": "string", "upload_url": "string (for large file upload)" }` |
| downloadFile            | GET      | /api/v1/drive/file/download/{file_id}    | `{}`                 | (Stream file content, JSON metadata) |
| createFolder            | POST     | /api/v1/drive/folder/create              | `{ "user_id": "string", "parent_folder_id": "string (optional, root if null)", "folder_name": "string" }` | `{ "folder_id": "string", "status": "string" }` |
| listFiles               | GET      | /api/v1/drive/folder/list/{folder_id}    | `{ "user_id": "string", "page": integer, "per_page": integer }` | `{ "files": [{}], "folders": [{}], "total_items": integer, "page": integer, "total_pages": integer }` |
| moveFile                | POST     | /api/v1/drive/item/move                  | `{ "user_id": "string", "item_id": "string (file or folder)", "target_folder_id": "string" }` | `{ "status": "string" }` |
| renameFile              | PUT      | /api/v1/drive/item/rename                | `{ "user_id": "string", "item_id": "string (file or folder)", "new_name": "string" }` | `{ "status": "string" }` |
| deleteFile              | DELETE   | /api/v1/drive/item/delete/{item_id}      | `{ "user_id": "string", "item_type": "file" "folder" }` | - |
| shareFile               | POST     | /api/v1/drive/item/share                 | `{ "user_id": "string", "item_id": "string (file or folder)", "share_with_user_ids": ["string"], "permission_level": "read" "write" }` | - |
| getSharedLink           | GET      | /api/v1/drive/item/share_link/{item_id}  | `{ "user_id": "string", "permission_level": "read" "write" }` | - |
| getFileVersionHistory   | GET      | /api/v1/drive/file/versions/{file_id}    | `{ "user_id": "string" }` | `{ "versions": [{}] }` |
| restoreFileVersion      | POST     | /api/v1/drive/file/restore_version       | `{ "user_id": "string", "file_id": "string", "version_id": "string" }` | `{ "status": "string" }` |
| searchFiles             | GET      | /api/v1/drive/file/search                | `{ "user_id": "string", "query": "string", "page": integer, "per_page": integer }` | `{ "files": [{}], "total_files": integer, "page": integer, "total_pages": integer }` |

### Database

#### MySQL Tables

#### Users
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| user_id    | VARCHAR(255) | YES | - | - |
| username   | VARCHAR(255) | NO | - | UNIQUE Index on username |

#### DriveFiles
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| file_id     | VARCHAR(255) | YES | - | - |
| user_id     | VARCHAR(255) | NO | YES | Index on user_id |
| file_name   | VARCHAR(255) | NO | - | - |
| file_type   | VARCHAR(255) | NO | - | - |
| file_size_bytes | BIGINT UNSIGNED | NO | - | - |
| object_storage_path | VARCHAR(255) | NO | - | - |
| parent_folder_id | VARCHAR(255) | NO | YES | Index on parent_folder_id |
| created_at  | TIMESTAMP | NO | - | - |
| updated_at  | TIMESTAMP | NO | - | - |

#### DriveFolders
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| folder_id   | VARCHAR(255) | YES | - | - |
| user_id     | VARCHAR(255) | NO | YES | Index on user_id |
| folder_name | VARCHAR(255) | NO | - | - |
| parent_folder_id | VARCHAR(255) | NO | YES | Index on parent_folder_id |
| created_at  | TIMESTAMP | NO | - | - |
| updated_at  | TIMESTAMP | NO | - | - |







# Chapter 17 - Proximity Service (Reiteration for Completeness)

A **Proximity Service** helps users find resources or other users within a geographical area.

## Services

| Service Name          | Description                                              |
|----------------------|------------------------------------------------------|
| `getNearbyPlaces`   | Retrieves a list of places of interest near a location. |
| `getNearbyUsers`    | Retrieves a list of users near a location.            |
| `registerPlace`     | Allows businesses or individuals to register a place.  |
| `updatePlaceLocation` | Updates the location of a registered place.           |
| `searchPlaces`      | Searches for places based on keywords and location.    |

## API Details & Schemas

| Service Name         | Req Type | API URL                                 | Request JSON Schema                                                                                     | Response JSON Schema                                                                    |
|----------------------|---------|-----------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| `getNearbyPlaces`   | GET     | `/api/v1/proximity/places/nearby`      | `{ "latitude": number, "longitude": number, "radius_km": number, "category": "string (optional)", "page": integer, "per_page": integer }` | `{ "places": [{}], "total_places": integer, "page": integer, "total_pages": integer }` |
| `getNearbyUsers`    | GET     | `/api/v1/proximity/users/nearby`       | `{ "latitude": number, "longitude": number, "radius_km": number, "user_category": "string (optional)", "page": integer, "per_page": integer }` | `{ "users": [{}], "total_users": integer, "page": integer, "total_pages": integer }` |
| `registerPlace`     | POST    | `/api/v1/proximity/place/register`     | `{ "place_name": "string", "latitude": number, "longitude": number, "category": "string", "description": "string", "contact_info": {} }` | `{ "place_id": "string", "status": "string" }` |
| `updatePlaceLocation` | PUT     | `/api/v1/proximity/place/location`      | `{ "place_id": "string", "latitude": number, "longitude": number }`                                   | `{ "status": "string" }` |
| `searchPlaces`      | GET     | `/api/v1/proximity/places/search`      | `{ "query": "string", "latitude": number, "longitude": number, "radius_km": number, "page": integer, "per_page": integer }` | `{ "places": [{}], "total_places": integer, "page": integer, "total_pages": integer }` |

## Database: PostGIS Extension for PostgreSQL

### PostgreSQL Tables (with PostGIS Extension)

#### Places Table

| Column Name    | Data Type                 | Primary Key | Foreign Key | Indexes                    |
|---------------|--------------------------|-------------|-------------|----------------------------|
| `place_id`    | VARCHAR(255)              | YES         |             |                            |
| `place_name`  | VARCHAR(255)              | NO          |             |                            |
| `category`    | VARCHAR(255)              | NO          |             | Index on `category`        |
| `description` | TEXT                       | NO          |             |                            |
| `contact_info`| JSONB                      | NO          |             |                            |
| `location`    | GEOGRAPHY(POINT, 4326)    | NO          |             | GIST Index on `location`   |
| `created_at`  | TIMESTAMP                  | NO          |             |                            |
| `updated_at`  | TIMESTAMP                  | NO          |             |                            |

#### Users Table (if needed for nearby users)

| Column Name             | Data Type               | Primary Key | Foreign Key | Indexes                         |
|------------------------|------------------------|-------------|-------------|---------------------------------|
| `user_id`             | VARCHAR(255)           | YES         |             |                                 |
| `username`            | VARCHAR(255)           | NO          |             | UNIQUE Index on `username`     |
| `user_category`       | VARCHAR(255)           | NO          |             | Index on `user_category`       |
| `last_location`       | GEOGRAPHY(POINT, 4326) | NO          |             | GIST Index on `last_location`  |
| `last_location_update` | TIMESTAMP              | NO          |             |                                 |










# Chapter 18 - Nearby Friends

A Nearby Friends feature allows users to discover friends who are geographically close.

## Services

| Service Name          | Description |
|----------------------|-------------|
| getNearbyFriends     | Retrieves a list of friends near a user's location. |
| updateLocation       | Updates a user's current location. |
| setLocationVisibility | Sets the visibility of a user's location to friends. |
| getFriendLocation    | Retrieves the location of a friend (if visible). |
| addFriend           | Sends a friend request to another user. |
| acceptFriendRequest | Accepts a friend request. |
| removeFriend        | Removes a friend from a user's friend list. |

## API Details & Schemas

| Service Name         | Req Type | API URL                                          | Request JSON Schema | Response JSON Schema |
|----------------------|---------|-------------------------------------------------|---------------------|----------------------|
| getNearbyFriends     | GET     | /api/v1/nearby_friends/friends/nearby          | `{ "user_id": "string", "latitude": number, "longitude": number, "radius_km": number, "page": integer, "per_page": integer }` | `{ "friends": [{}], "total_friends": integer, "page": integer, "total_pages": integer }` |
| updateLocation       | POST    | /api/v1/nearby_friends/location/update         | `{ "user_id": "string", "latitude": number, "longitude": number }` | `{ "status": "string" }` |
| setLocationVisibility | PUT     | /api/v1/nearby_friends/location/visibility     | `{ "user_id": "string", "visibility": "friends" | "public" | "private" }` | `{ "status": "string" }` |
| getFriendLocation    | GET     | /api/v1/nearby_friends/friend/location/{friend_user_id} | `{ "user_id": "string" }` | `{ "latitude": number, "longitude": number, "visibility": "friends" | "public" | "private" }` |
| addFriend           | POST    | /api/v1/nearby_friends/friend/add              | `{ "requesting_user_id": "string", "target_user_id": "string" }` | `{ "status": "string" }` |
| acceptFriendRequest | POST    | /api/v1/nearby_friends/friend/accept           | `{ "request_id": "string" }` | `{ "status": "string" }` |
| removeFriend        | POST    | /api/v1/nearby_friends/friend/remove           | `{ "user_id": "string", "friend_user_id": "string" }` | `{ "status": "string" }` |

## Database: PostGIS extension for PostgreSQL

### PostgreSQL Tables (with PostGIS extension)

#### Users Table
| Column Name           | Data Type                        | Primary Key | Foreign Key | Indexes |
|-----------------------|---------------------------------|-------------|-------------|---------|
| user_id              | VARCHAR(255)                    | YES         |             |         |
| username            | VARCHAR(255)                    | NO          |             | UNIQUE Index on username |
| last_location        | GEOGRAPHY(POINT, 4326)         | NO          |             | GIST Index on last_location |
| last_location_update | TIMESTAMP                      | NO          |             |         |
| location_visibility  | ENUM('friends', 'public', 'private') | NO          |             |         |

#### Friendships Table
| Column Name     | Data Type         | Primary Key | Foreign Key | Indexes |
|---------------|-----------------|-------------|-------------|---------|
| friendship_id | INT AUTO_INCREMENT | YES         |             |         |
| user_id_1    | VARCHAR(255)       | NO          | YES         | Index on user_id_1 |
| user_id_2    | VARCHAR(255)       | NO          | YES         | Index on user_id_2 |
| status       | ENUM('pending', 'accepted', 'rejected') | NO          |             | Index on status |
| created_at   | TIMESTAMP          | NO          |             |         |

---










# Chapter 19 - Google Maps

Design a map and navigation service like Google Maps. This is a very broad topic, focusing on core functionalities.

## Services (Core Functionalities)

| Service Name       | Description |
|-------------------|-------------|
| getMapTiles      | Retrieves map tiles for a given area and zoom level. |
| searchPlaces     | Searches for places of interest. |
| getDirections    | Calculates directions between two locations. |
| getPlaceDetails  | Retrieves detailed information about a place. |
| reportTrafficData | Allows reporting of real-time traffic conditions. |
| getTrafficData   | Retrieves real-time traffic data for an area. |

## API Details & Schemas

| Service Name       | Req Type | API URL                              | Request JSON Schema | Response JSON Schema |
|-------------------|---------|--------------------------------------|---------------------|----------------------|
| getMapTiles      | GET     | /api/v1/maps/tiles/{z}/{x}/{y}.png | {} (Tile coordinates in URL path) | (Image/PNG Map Tile) |
| searchPlaces     | GET     | /api/v1/maps/places/search         | `{ "query": "string", "latitude": number, "longitude": number, "radius_km": number, "page": integer, "per_page": integer }` | `{ "places": [{}], "total_places": integer, "page": integer, "total_pages": integer }` |
| getDirections    | GET     | /api/v1/maps/directions           | `{ "origin_latitude": number, "origin_longitude": number, "destination_latitude": number, "destination_longitude": number, "travel_mode": "driving" | "walking" }` | `{ "route": [{}] }` |
| getPlaceDetails  | GET     | /api/v1/maps/place/details/{place_id} | `{}` | `{ "place_id": "string", "name": "string", "location": {}, "address": "string", "rating": number, "reviews": [{}] }` |
| reportTrafficData | POST    | /api/v1/maps/traffic/report      | `{ "road_segment_id": "string", "traffic_level": "low" | "medium" | "high" }` | `{ "status": "string" }` |
| getTrafficData   | GET     | /api/v1/maps/traffic/area        | `{ "southwest_latitude": number, "southwest_longitude": number, "northeast_latitude": number, "northeast_longitude": number }` | `{ "traffic_incidents": [{}] }` |

## Database Considerations

- **Map Tile Storage:** Object Storage (AWS S3, Google Cloud Storage) with CDN for caching.
- **Places Data:** PostgreSQL with PostGIS for geospatial search.
- **Routing Data:** Graph database (Neo4j) for pathfinding.
- **Traffic Data:** Time-series database (InfluxDB, TimescaleDB) or NoSQL (Cassandra) for real-time updates.

### Places Table (PostgreSQL with PostGIS)
| Column Name     | Data Type       | Primary Key | Foreign Key | Indexes |
|---------------|---------------|-------------|-------------|---------|
| place_id      | VARCHAR(255)  | YES         |             |         |
| place_name    | VARCHAR(255)  | NO          |             |         |
| category      | VARCHAR(255)  | NO          |             | Index on category |
| location      | GEOGRAPHY(POINT, 4326) | NO  |             | GIST Index on location |







# Chapter 23 - Hotel Reservation System

A Hotel Reservation System allows users to search for, book, and manage hotel reservations.

## Services

| Service Name         | Description                                      |
|----------------------|--------------------------------------------------|
| searchHotels        | Searches for available hotels based on criteria. |
| getHotelDetails     | Retrieves detailed information about a specific hotel. |
| makeReservation     | Creates a new hotel reservation.                 |
| cancelReservation   | Cancels an existing reservation.                  |
| getUserReservations | Retrieves a user's reservation history.          |
| getRoomAvailability | Checks room availability for a hotel and date range. |
| addHotel           | Allows admin to add new hotels to the system.     |
| updateHotelInfo    | Allows admin to update hotel information.         |
| manageRoomTypes    | Allows admin to manage room types for hotels.     |

## API Details & Schemas

| Service Name         | Req Type | API URL | Request JSON Schema | Response JSON Schema |
|----------------------|---------|---------|---------------------|----------------------|
| searchHotels        | GET     | `/api/v1/hotel_reservation/hotels/search` | `{ "city": "string", "check_in_date": "date (YYYY-MM-DD)", "check_out_date": "date (YYYY-MM-DD)", "num_guests": integer, "amenities": ["string (optional)"], "price_range_min": number (optional), "price_range_max": number (optional), "page": integer, "per_page": integer }` | `{ "hotels": [{}], "total_hotels": integer, "page": integer, "total_pages": integer }` |
| getHotelDetails     | GET     | `/api/v1/hotel_reservation/hotel/{hotel_id}` | `{}` | `{ "hotel_id": "string", "hotel_name": "string", "address": "string", "city": "string", "description": "string", "amenities": ["string"], "room_types": [{}], "rating": number, "photos": ["string"] }` |
| makeReservation     | POST    | `/api/v1/hotel_reservation/reservation/make` | `{ "user_id": "string", "hotel_id": "string", "room_type_id": "string", "check_in_date": "date (YYYY-MM-DD)", "check_out_date": "date (YYYY-MM-DD)", "num_guests": integer, "guest_details": [{ "name": "string", "contact_number": "string" }] }` | `{ "reservation_id": "string", "status": "string", "confirmation_number": "string" }` |
| cancelReservation   | POST    | `/api/v1/hotel_reservation/reservation/cancel` | `{ "reservation_id": "string", "user_id": "string" }` | `{ "reservation_id": "string", "status": "string", "cancellation_confirmation": "string", "refund_amount": number (optional) }` |
| getUserReservations | GET     | `/api/v1/hotel_reservation/reservations/user/{user_id}` | `{ "page": integer, "per_page": integer, "status_filter": "active" "cancelled" }` | ... |
| getRoomAvailability | GET     | `/api/v1/hotel_reservation/room_availability` | `{ "hotel_id": "string", "room_type_id": "string", "check_in_date": "date (YYYY-MM-DD)", "check_out_date": "date (YYYY-MM-DD)" }` | `{ "hotel_id": "string", "room_type_id": "string", "available_rooms": integer }` |
| addHotel           | POST    | `/api/v1/hotel_reservation/admin/hotel/add` | `{ "hotel_name": "string", "address": "string", "city": "string", "description": "string", "amenities": ["string"], "photos": ["string"] }` | `{ "hotel_id": "string", "status": "string" }` |
| updateHotelInfo    | PUT     | `/api/v1/hotel_reservation/admin/hotel/update/{hotel_id}` | `{ "hotel_name": "string (optional)", "address": "string (optional)", "city": "string (optional)", "description": "string (optional)", "amenities": ["string (optional)"], "photos": ["string (optional)"] }` | `{ "hotel_id": "string", "status": "string" }` |
| manageRoomTypes    | POST/PUT| `/api/v1/hotel_reservation/admin/room_types/{hotel_id}` | `[ { "room_type_id": "string (optional for POST)", "room_type_name": "string", "description": "string", "price_per_night": number, "max_guests": integer, "amenities": ["string"], "available_count": integer } ]` | `{ "hotel_id": "string", "status": "string", "updated_room_types": ["string"] }` |

## Database: MySQL

### Tables

#### Hotels
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| hotel_id   | VARCHAR(255) | YES | - | - |
| hotel_name | VARCHAR(255) | NO | - | Index on hotel_name, city |
| address    | TEXT | NO | - | - |
| city       | VARCHAR(255) | NO | - | Index on city |
| description | TEXT | NO | - | - |
| rating     | DECIMAL(2,1) | NO | - | - |
| amenities  | JSON | NO | - | - |
| photos     | JSON | NO | - | - |

#### RoomTypes
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| room_type_id | VARCHAR(255) | YES | - | - |
| hotel_id | VARCHAR(255) | NO | YES | Index on hotel_id |
| room_type_name | VARCHAR(255) | NO | - | - |
| description | TEXT | NO | - | - |
| price_per_night | DECIMAL(10,2) | NO | - | - |
| max_guests | INT | NO | - | - |
| amenities | JSON | NO | - | - |
| available_count | INT | NO | - | - |


#### Reservations
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| reservation_id | VARCHAR(255) | YES | - | - |
| user_id | VARCHAR(255) | NO | YES | Index on user_id |
| hotel_id | VARCHAR(255) | NO | YES | Index on hotel_id |
| room_type_id | VARCHAR(255) | NO | YES | Index on room_type_id |
| check_in_date | DATE | NO | - | Index on check_in_date |
| check_out_date | DATE | NO | - | Index on check_out_date |
| num_guests | INT | NO | - | - |
| guest_details | JSON | NO | - | - |
| status | ENUM('pending', 'confirmed', 'cancelled', 'completed') | NO | - | Index on status |
| confirmation_number | VARCHAR(255) | NO | - | UNIQUE Index on confirmation_number |
| cancellation_confirmation | VARCHAR(255) | NO | - | - |
| refund_amount | DECIMAL(10,2) | NO | - | - |
| created_at | TIMESTAMP | NO | - | - |
| updated_at | TIMESTAMP | NO | - | - |

#### RoomAvailability
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| availability_id | INT AUTO_INCREMENT | YES | - | UNIQUE Index on (hotel_id, room_type_id, date) |
| hotel_id | VARCHAR(255) | NO | YES | - |
| room_type_id | VARCHAR(255) | NO | YES | - |
| date | DATE | NO | - | Index on date |
| available_rooms | INT | NO | - | - |

#### Users
| Column Name | Data Type | Primary Key | Foreign Key | Indexes |
|-------------|----------|-------------|-------------|---------|
| user_id | VARCHAR(255) | YES | - | - |
| username | VARCHAR(255) | NO | - | UNIQUE Index on username |
| email | VARCHAR(255) | NO | - | UNIQUE Index on email |
| phone_number | VARCHAR(255) | NO | - | UNIQUE Index on phone_number |
| password_hash | VARCHAR(255) | NO | - | - |
| payment_info | JSON | NO | - | - |









