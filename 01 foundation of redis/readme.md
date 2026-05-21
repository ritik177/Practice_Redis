# Redis Foundation

Redis stands for **Remote Dictionary Server**. It is a fast, in-memory data store that keeps data mostly in RAM. Redis is commonly used for caching, session storage, queues, rate limiting, real-time counters, leaderboards, pub/sub messaging, and temporary data storage.

In simple words:

```txt
Redis = super fast key-value database
```

Example:

```txt
key   = user:1:name
value = Ritik
```

Redis is not always a replacement for databases like MySQL, PostgreSQL, or MongoDB. In most real-world applications, Redis is used together with a main database to make the application faster and reduce database load.

---

## What Is Redis?

Redis is a **NoSQL in-memory data structure store**.

It is not just a simple key-value store. Redis supports multiple data structures:

| Data Type | Use Case |
| --- | --- |
| String | Cached value, token, config, counter |
| Hash | User profile, object-like data |
| List | Queue, recent activities |
| Set | Unique values, tags, followers |
| Sorted Set | Leaderboard, ranking, score-based data |
| Stream | Event log, message processing |
| Pub/Sub | Real-time messaging |

Redis stores data in memory, so read and write operations are very fast.

---

## Why Do We Need Redis?

Suppose an API repeatedly fetches the same data from the database:

```txt
User opens home page -> API hits database
Another user opens home page -> API hits database again
1000 users open home page -> database gets 1000 similar requests
```

If the data is read frequently and does not change every second, we can cache it in Redis.

Flow:

```txt
Request received
  -> Is data available in Redis? Return it from Redis
  -> Is data missing in Redis? Fetch from database, save in Redis, return response
```

This helps with:

- faster response time
- reduced database load
- better handling of high traffic
- avoiding repeated computation

---

## Where Do We Use Redis?

Redis is useful when we need fast access, temporary storage, or real-time behavior.

Common use cases:

1. **Caching**

   Product details, user profiles, homepage data, configuration, banner messages, etc. can be cached in Redis.

2. **Session Storage**

   Login sessions, auth tokens, OTP verification state, and cart sessions can be stored in Redis.

3. **Rate Limiting**

   Example: one user can make only 100 API requests in 1 minute. Redis counters and expiry make this easy to implement.

4. **Real-Time Counters**

   Redis is useful for likes, views, active users, page visits, download counts, and similar counters.

5. **Queues**

   Background jobs like sending emails, processing notifications, generating reports, and image processing can be managed with queues.

6. **Leaderboards**

   Gaming scores, ranking systems, and top user lists can be built with Redis Sorted Sets.

7. **Pub/Sub Messaging**

   Chat apps, notifications, and live updates can use Redis publish/subscribe messaging.

8. **Temporary Data**

   OTPs, password reset tokens, and email verification tokens can be stored with expiry.

---

## Redis Vs Normal Database

| Point | Redis | Normal Database |
| --- | --- | --- |
| Storage | Mostly RAM | Disk |
| Speed | Very fast | Comparatively slower |
| Data model | Key-value/data structures | Tables/documents |
| Best for | Cache, temp data, real-time data | Permanent business data |
| Querying | Key-based access | Complex queries supported |
| Persistence | Optional/supported | Core feature |

Important point:

```txt
Redis is fast, but it is usually not a full replacement for the main database.
```

The main database is usually the source of truth. Redis is often used as a cache or supporting system.

---

## Basic Redis Commands

### String Commands

```bash
SET name "Ritik"
GET name
DEL name
```

### Expiry

```bash
SET otp:1234 "987654" EX 300
TTL otp:1234
```

`EX 300` means the value will expire after 300 seconds.

### Counter

```bash
SET views 0
INCR views
INCR views
GET views
```

### Hash

```bash
HSET user:1 name "Ritik" role "developer"
HGET user:1 name
HGETALL user:1
```

### List

```bash
LPUSH tasks "send-email"
LPUSH tasks "generate-report"
RPOP tasks
```

### Set

```bash
SADD skills "nodejs"
SADD skills "redis"
SMEMBERS skills
```

### Sorted Set

```bash
ZADD leaderboard 100 "Ritik"
ZADD leaderboard 200 "Aman"
ZRANGE leaderboard 0 -1 WITHSCORES
```

---

## Important Redis Concepts

### 1. Key

In Redis, every value is accessed through a key.

Example:

```txt
user:1
product:45
session:abc123
```

Good key naming is important. A common pattern is:

```txt
entity:id:field
```

Example:

```txt
user:101:profile
product:55:details
```

### 2. TTL

TTL stands for **Time To Live**. It defines how many seconds a key will exist before it expires automatically.

Example:

```bash
SET reset-token:abc "user-1" EX 900
```

This token will be deleted after 15 minutes.

### 3. Cache Hit

A cache hit happens when the requested data is found in Redis.

```txt
Request -> Redis -> Data found -> Fast response
```

### 4. Cache Miss

A cache miss happens when the requested data is not found in Redis.

```txt
Request -> Redis -> Data not found -> Database -> Save in Redis -> Response
```

### 5. Eviction

When Redis memory starts filling up, Redis can remove old or less important keys depending on the configured eviction policy.

Common policies:

- `noeviction`
- `allkeys-lru`
- `volatile-lru`
- `allkeys-random`
- `volatile-ttl`

### 6. Persistence

Redis is in-memory, but it also supports persistence.

Main persistence options:

- **RDB**: Point-in-time snapshots.
- **AOF**: Maintains a log of write operations.

RDB is useful for faster recovery. AOF is useful for better durability.

---

## Common Redis Patterns

### Cache-Aside Pattern

The application first checks Redis. If the data is missing, it fetches data from the database and stores it in Redis.

```txt
App -> Redis
  -> found: return data
  -> not found: fetch from DB -> store in Redis -> return data
```

This is the most common caching pattern.

### Write-Through Cache

Data is written to both the database and the cache.

```txt
Write request -> DB update -> Redis update
```

### Cache Invalidation

When data is updated in the database, the Redis cache should also be deleted or updated. If invalidation is not handled correctly, users may receive stale data.

Example:

```txt
Product price is updated in the database
Redis still contains the old price
User may see the wrong price
```

Solution:

```txt
Update DB -> Delete/Update Redis key
```

---

## Interview Questions And Answers

### 1. What is Redis?

Redis is an open-source, in-memory NoSQL data store that supports key-value storage and multiple data structures. It is commonly used for caching, session storage, queues, rate limiting, counters, pub/sub, and real-time systems.

### 2. Why is Redis fast?

Redis is fast because it stores data mostly in RAM. Disk-based databases need disk access for many read/write operations, which is slower than memory access. Redis also uses efficient data structures and simple command processing.

### 3. Is Redis a database or cache?

Redis can be used as both a database and a cache. In many practical applications, Redis is mainly used as a cache or supporting data store, while the main database remains the source of truth.

### 4. What are common Redis use cases?

Common use cases are:

- caching
- session management
- OTP and temporary token storage
- rate limiting
- queues
- leaderboards
- real-time counters
- pub/sub messaging

### 5. What is TTL in Redis?

TTL stands for Time To Live. It defines how long a Redis key should exist before it expires automatically. Example: storing an OTP for 5 minutes.

```bash
SET otp:123 "456789" EX 300
```

### 6. What is cache hit and cache miss?

A cache hit means the requested data was found in Redis.

A cache miss means the requested data was not found in Redis, so the application needs to fetch it from the database or original source.

### 7. What is cache invalidation?

Cache invalidation means deleting or updating old/stale cache data. When data changes in the main database, the Redis cache should also be updated or deleted.

### 8. What data types does Redis support?

Redis supports Strings, Hashes, Lists, Sets, Sorted Sets, Streams, Bitmaps, HyperLogLog, and Geospatial indexes.

### 9. What is the difference between Redis String and Hash?

A String stores a simple value.

Example:

```bash
SET user:1:name "Ritik"
```

A Hash stores object-like data.

Example:

```bash
HSET user:1 name "Ritik" age "22"
```

For data like a user profile, a Hash can be a better choice.

### 10. What is Redis Pub/Sub?

Pub/Sub stands for Publish/Subscribe. In this pattern, a publisher sends a message to a channel, and subscribers of that channel receive the message. It is useful for real-time notifications, chat, and live updates.

### 11. What is Redis persistence?

Redis stores data in memory, but it can also persist data to disk. The main Redis persistence options are RDB snapshots and AOF logs.

### 12. What is the difference between RDB and AOF?

| Point | RDB | AOF |
| --- | --- | --- |
| Meaning | Snapshot persistence | Append-only command log |
| Performance | Usually faster | Slightly more overhead |
| Durability | Data after the last snapshot may be lost | Better durability |
| File size | Smaller | Larger |

### 13. What happens if Redis memory is full?

Redis can evict keys based on the configured `maxmemory-policy`. If the policy is `noeviction`, write commands may return errors when memory is full.

### 14. What is LRU?

LRU stands for Least Recently Used. With this policy, Redis removes keys that have not been used recently.

### 15. What is Redis Sorted Set?

A Sorted Set stores unique members with scores. Members are sorted by score. It is useful for leaderboards and ranking systems.

Example:

```bash
ZADD leaderboard 500 "player1"
ZADD leaderboard 800 "player2"
```

### 16. How can Redis be used for rate limiting?

Redis can store request counts for a user or IP address and set an expiry on that key.

Example:

```txt
rate:user:101 = request count for 1 minute
```

On every request, the counter is incremented. If the limit is crossed, the API can block the request.

### 17. How can Redis be used for OTP?

An OTP can be stored in Redis with an expiry.

```bash
SET otp:mobile:9999999999 "123456" EX 300
```

After 5 minutes, the OTP will expire automatically.

### 18. What is Redis Cluster?

Redis Cluster distributes data across multiple Redis nodes. It helps with horizontal scaling and high availability.

### 19. What is Redis replication?

Replication copies data from a primary Redis server to replica servers. If the primary fails, a replica can help with failover.

### 20. What is the difference between Redis and MongoDB?

| Point | Redis | MongoDB |
| --- | --- | --- |
| Type | In-memory key-value/data structure store | Document database |
| Best for | Cache, sessions, counters, queues | Persistent document storage |
| Query style | Key-based commands | Rich document queries |
| Storage | Mostly memory | Disk-first storage |

### 21. Can Redis store JSON?

Basic Redis can store JSON as a string.

Example:

```bash
SET user:1 '{"name":"Ritik","role":"developer"}'
```

To update or query specific JSON fields, RedisJSON can be used.

### 22. What are the disadvantages of Redis?

Some Redis limitations are:

- RAM is expensive, so very large datasets can become costly.
- Complex querying is not like SQL or MongoDB.
- Cache invalidation must be handled carefully.
- Wrong persistence configuration can create data loss risk.
- A single Redis instance can become a point of failure unless replication or clustering is configured.

### 23. When should we not use Redis?

Redis may not be the right choice when:

- the data is very large and cannot fit in memory
- complex joins or queries are required
- a long-term source of truth database is needed
- data is rarely accessed and speed is not critical

### 24. What is a good Redis key naming practice?

Use readable and consistent key names.

Example:

```txt
user:101:profile
product:55:details
cart:user:101
otp:mobile:9999999999
```

Using colon `:` as a separator is a common convention.

### 25. How do you delete a Redis key?

```bash
DEL user:101:profile
```

You can also delete multiple keys:

```bash
DEL key1 key2 key3
```

### 26. How do you check if a key exists?

```bash
EXISTS user:101:profile
```

Return:

```txt
1 means key exists
0 means key does not exist
```

### 27. How do you check expiry of a key?

```bash
TTL user:101:profile
```

Common output:

```txt
positive number = seconds remaining
-1 = key exists but has no expiry
-2 = key does not exist
```

### 28. What is an atomic operation in Redis?

An atomic operation means the operation either completes fully or does not happen at all. Redis commands are generally atomic. Example: `INCR views` safely increments a counter, even if multiple requests are happening at the same time.

### 29. Why is Redis useful in Node.js applications?

Redis is useful in Node.js applications because APIs often need to handle high traffic. Redis provides a fast and simple solution for caching, sessions, queues, rate limiting, and real-time features.

### 30. Explain Redis in one interview answer.

Redis is an open-source in-memory NoSQL data store that supports key-value storage and rich data structures. It is very fast because it keeps data in RAM. Redis is used for caching, sessions, rate limiting, queues, counters, leaderboards, pub/sub, and temporary data like OTPs or tokens. In production, Redis is often used together with a main database to reduce database load and improve application response time.

---

## Short Revision

- Redis is a fast in-memory data store.
- Redis is key-value based, but it supports multiple data structures.
- Redis is commonly used for cache, sessions, queues, counters, leaderboards, and rate limiting.
- TTL allows keys to expire automatically.
- Cache hit means the data was found in Redis.
- Cache miss means the data was not found in Redis.
- Cache invalidation is important to avoid stale data.
- Redis persistence is possible with RDB and AOF.
- Redis is usually not a full replacement for the main database.
