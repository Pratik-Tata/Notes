
---

## 🧱 **LEVEL 0: Fundamentals (You Cannot Skip This)**

|Topic|What to Know|
|---|---|
|**Latency, Throughput, Availability**|Definitions, trade-offs, tail latency|
|**CAP Theorem**|Consistency, Availability, Partition tolerance—pick 2|
|**Scalability Types**|Vertical vs Horizontal|
|**Load Balancing Basics**|Round Robin, Consistent Hashing, Health Checks|
|**Caching**|CDN vs App-level, TTL, cache invalidation|
|**Database Scaling**|Indexing, Sharding, Replication (master/slave, leader/follower)|

---

## 🔧 **LEVEL 1: High-Level Design (HLD)**

|Topic|What to Know|
|---|---|
|**System Components**|Clients, APIs, Load Balancers, Services, DBs, Queues|
|**API Design**|REST, GraphQL, versioning, pagination|
|**Authentication & Authorization**|OAuth2, JWT, sessions vs tokens|
|**Rate Limiting**|Token bucket, leaky bucket, redis-backed limiter|
|**Asynchronous Communication**|Message Queues (Kafka, RabbitMQ), retries, dead-letter|
|**Microservices**|Service Registry, Circuit Breakers, API Gateway, Saga pattern|
|**Event-Driven Design**|Event sourcing vs CQRS vs pub-sub|

✅ You should be able to whiteboard **a URL shortener, file uploader, or ride-hailing app** by this point.

---

## 🔩 **LEVEL 2: Low-Level Design (LLD)**

|Topic|What to Know|
|---|---|
|**OOP & Design Patterns**|SOLID, Factory, Strategy, Singleton, Builder|
|**Class Diagrams**|Class relationships, interfaces, inheritance, composition|
|**Entity Modeling**|ERD, normalization, foreign keys, many-to-many joins|
|**Concurrency**|Locks, threads, producer-consumer, thread pools, race conditions|
|**Scalable APIs**|Pagination, filtering, sorting, bulk upload/download|
|**Package Design**|Layered architecture, hexagonal/clean architecture|
|**Testability**|Unit testing, mocking, test-driven design|

✅ You should now be able to design a **rate limiter class**, a **thread-safe queue**, or a **Job Scheduler**.

---

## 🏗️ **LEVEL 3: Real-World Design Topics**

|Topic|What to Know|
|---|---|
|**Consistency Models**|Strong vs eventual, quorum-based reads/writes|
|**Distributed Transactions**|2PC, Saga, Idempotency|
|**Storage Engines**|LSM Tree, B+Tree, Append-only logs|
|**Search Systems**|Inverted indexes, Elasticsearch basics|
|**Observability**|Logs, metrics, traces, OpenTelemetry|
|**Deployments**|Blue-green, canary, rolling, rollback|
|**Data Privacy**|GDPR basics, data masking, audit logs|

✅ Now you’re dangerous. You can design **Dropbox, WhatsApp, or LinkedIn feed** level systems.

---

## ☁️ **LEVEL 4: Infra & Scalability Mastery**

|Topic|What to Know|
|---|---|
|**Service Discovery**|DNS, Consul, Eureka, etcd|
|**Containerization**|Docker, Kubernetes basics (pods, services, deployments)|
|**CDN & Edge Caching**|Cloudflare, Akamai, signed URLs|
|**Scaling DBs**|Partitioning (range/hash), replication lag, quorum reads|
|**Load Shedding & Backpressure**|Timeout strategies, circuit breakers|
|**Data Pipelines**|Batch vs stream, Kafka/Flink/Spark|
|**Geo-distribution**|Data locality, multi-region deployment, latency trade-offs|

✅ At this point, you’re thinking like a **solution architect**, not just a dev.

---

## 🔥 Bonus: Interview-Focused Topics (Google/Amazon/Meta-style)

|Scenario|Must Know|
|---|---|
|**Design YouTube**|Video storage, encoding pipeline, CDN|
|**Design WhatsApp**|End-to-end encryption, message sync, delivery guarantees|
|**Design Uber**|Geo-indexing, GPS updates, Kafka for location events|
|**Design Twitter Feed**|Fan-out, read vs write optimization, caching layers|
|**Design Instagram Stories**|Object storage, ephemeral content, real-time sync|

---

## 🧭 TL;DR – System Design Syllabus (By Priority)

|Tier|Focus|
|---|---|
|🥇 Must Know|Caching, Load Balancing, API Design, DB scaling, Queueing|
|🥈 High Leverage|Auth, Observability, Deployment patterns, Event-driven systems|
|🥉 Bonus Level|Infra mastery (K8s, Kafka internals), Data-intensive flows, GDPR stuff|


---

# ⭐ 1) **Primary Key (PK)**

This is **database-local** identity.

### What it is:

- Uniquely identifies a row _inside_ a table
    
- No meaning outside that service’s DB
    
- Often auto-increment (`bigint`) or UUID
    

### Scope:

**ONLY that table in that service.**

### Example:

`order.id = 12345`

It is **not** used across services.  
It is **not** used across events.  
It is **not** used across the system.

It’s a _data structure detail_, nothing more.

---

# ⭐ 2) **UUID**

You use this when you need an ID that is:

- **Globally unique** (not just database unique)
    
- **Generated without DB calls** (no roundtrip)
    
- **Stable across services**
    
- **Safe for distributed systems**
    

### Why UUID when PK exists?

Because PK lives inside the DB.  
UUID lives **outside**, independent of storage.

Use UUID when:

- client generates ID before hitting backend
    
- two services need the same identifier
    
- events need a global ID
    
- scaling horizontally and DB sequences become bottlenecks
    
- you want request idempotency
    
- multi-region architecture (AWS/GCP style)
    

**Rule:**  
If the ID leaves your service boundary → use UUID.

---

# ⭐ 3) **DTID (Digital Transaction ID)**

Think of a DTID as:

> “This is the ID of THIS business action.”

This is a **business-level identifier**, NOT a technical identifier.

### Purpose:

- Represents a _transaction_ or _business event chain_
    
- Travel across all services that participate
    
- Lets you link everything that happened for this _transaction_
    

### Example scenario:

User pays for something:

- Service A: creates payment request → DTID = X
    
- Service B: processes payment → uses same DTID = X
    
- Service C: updates order → uses DTID = X
    

### Can two records have the same DTID?

YES, absolutely — if they belong to the **same business transaction**.

For example:

- stored → DTID = X
    
- initiated → DTID = X
    
- completed → DTID = X
    

That is CORRECT.

### Will Service A → B → C events have the same DTID?

YES — if they are part of the **same transaction**.

DTID is the “lifecycle ID.”

---

# ⭐ 4) **Correlation ID**

This is different from DTID, though people confuse them.

### Correlation ID answer:

> "Which events and logs belong to this SAME REQUEST FLOW?"

### Scope:

- **Technical tracing**
    
- Ties logs together across services
    
- Helps debugging
    
- Used by tracing systems like Zipkin, Jaeger
    

### Rule:

Every request → 1 correlation ID.

### Example:

Service A receives a request → correlation-id = ABC  
Service B called → ABC  
Service C called → ABC

So your **logs** show the entire chain under correlation id ABC.

---

# ⭐ DTID vs Correlation ID (the god-level clarity)

|Purpose|DTID|Correlation ID|
|---|---|---|
|Meaning|Business transaction ID|Technical trace ID|
|Scope|Across all services for same transaction|Only for this request chain|
|Stability|Same for entire lifecycle|New one per request entry|
|Who generates|Business logic (A starts transaction)|API gateway / Service A|
|Example|payment_id / transaction_id|X-B3-TraceId|

Often, one business transaction (DTID) may involve **multiple requests** → each request gets its own correlation ID.

---

# ⭐ Your final concrete answers

### **Q: Can DB records like stored / initiated / completed share same DTID?**

✔ **YES.**  
They belong to the **same business transaction**.  
This is correct design.

### **Q: Can they share same Correlation ID?**

❌ **No, not necessarily.**  
Each request generates a new correlation ID.

**But** if all those states are updated in **one request**, then yes—correlation ID will be same.

Usually:

- DTID: same
    
- correlation ID: different (each step is a separate request/event)
    

---

# ⭐ TL;DR (your “fren cheat sheet”)

- **PK** → local row identity
    
- **UUID** → globally unique ID for distributed components
    
- **DTID** → _business_ transaction identity across lifecycle
    
- **Correlation ID** → _request trace_ identity across micro-services
    

---


- **High-throughput architecture basics**
    
- **Event loop model**
    
- **Async/await internals**
    
- **Why infinite loops break JS**
    
- **Java vs JS async**
    
- **Coroutines**
    

This is everything you learned tonight, organized like a cheat sheet.

---

# 🌟 **NOTES — High Throughput + Async + Event Loop + Coroutines**

_(Your midnight journey, but clean.)_

---

# 1️⃣ **High Throughput Systems (Context of BloomConnect)**

### **Hot/Cold Bucket Pattern**

- Maintain two data structures:
    
    - **HOT** → receives live updates
        
    - **COLD** → old bucket being flushed to DB
        
- Swap atomically every X seconds.
    
- HOT immediately starts receiving updates again.
    
- COLD persists in batch.
    

### **Why?**

- Avoids 1000s of DB writes/sec.
    
- High throughput, minimal locking.
    

### **When to use Redis?**

- If you need global monotonic counters.
    
- Not needed if local shard counters are enough.
    

### **AtomicInteger / LongAdder**

- Needed if multiple threads update same counter.
    
- Avoidable if each shard has 1 worker (single-threaded).
    

---

# 2️⃣ **JavaScript Event Loop — The Core**

### **JS is single-threaded**

- Only ONE thread executes JS code.
    
- No parallel JS execution.
    

### **Runtime (browser/Node) is multi-threaded**

- Handles I/O, timers, network, filesystem, crypto.
    
- After async work finishes, pushes callbacks into the event loop queue.
    

### **Event Loop Model**

Think of it like:

```
while (queue not empty) {
   task = queue.dequeue();
   execute(task);
}
```

But with two queues:

### **Macrotask Queue**

- setTimeout
    
- setInterval
    
- DOM events
    
- I/O callbacks
    

### **Microtask Queue**

- Promise.then
    
- async/await continuations
    
- queueMicrotask
    
- MutationObserver
    

**Order:**

1. Run 1 macrotask
    
2. Run ALL microtasks
    
3. Render
    
4. Repeat
    

---

# 3️⃣ **Async/Await — What Actually Happens**

### **Key Truths**

✔️ `async` alone does NOTHING async  
✔️ `await` **pauses** the function  
✔️ When the awaited Promise resolves, JS schedules a **microtask**:

```
resume function
```

✔️ JS only resumes the function **when the call stack is empty**

### **Async function flow**

1. Runs synchronously until first `await`
    
2. Suspends → returns Promise
    
3. Event loop resumes it later via microtask
    

---

# 4️⃣ **Why Infinite Loops Break Async**

Example:

```js
async function foo() {
    await fetch();
    console.log("after await");
}

foo();

while(true) {}
```

- `foo()` starts → hits await → pauses
    
- Infinite loop hogs JS thread
    
- Event loop never gets a turn
    
- Microtask with “resume foo” is never executed
    
- `"after await"` **never prints**
    

💡 **Event loop requires the thread to be free.**

---

# 5️⃣ **Java vs JavaScript Async**

### **Java**

- Multi-threaded
    
- CompletableFutures / Virtual threads use thread pools
    
- Async work continues even if main thread is blocked
    

### **JavaScript**

- Single-threaded
    
- Async functions depend on the event loop
    
- If JS thread is blocked → ALL async functions freeze
    

**Difference in one line:**

> **Java async = true concurrency  
> JS async = coroutine scheduling on one thread**

---

# 6️⃣ **Coroutines — The Big Picture**

### **What a coroutine is**

A function that can:

- **pause**
    
- **yield**
    
- **resume later**
    
- **without losing state**
    

### **Coroutines ≠ Async**

Coroutines are the underlying mechanism.  
Async/await is one way to _use_ coroutines.

### **Coroutines in different languages**

**JavaScript**

- async/await is coroutine-based
    
- Scheduler = event loop
    
- Execution = single thread
    

**Kotlin**

- First-class coroutines
    
- Structured concurrency
    
- Run on ANY dispatcher (threads, IO pool)
    
- True parallelism
    

**Python**

- async = coroutine
    
- Scheduler = asyncio event loop
    

**Rust**

- async fn → compiled into coroutine-like state machine
    

**C#**

- async/await → built on state machines (similar to coroutines)
    

### **Common theme**

All async/await languages are using **coroutine-like mechanics**, only the scheduler differs:

JS → event loop  
Kotlin/Java → thread pools  
Python → asyncio loop  
Rust → executor

---

# 7️⃣ **Final Mental Models**

### **Event loop runs ONLY async tasks**

Regular function calls run immediately, not in the event loop.

### **Await always means:**

> pause here → resume later → resume only when JS thread free

### **Coroutines = async without threads**

But some languages **schedule** coroutines on multiple threads (Kotlin, Java virtual threads, Rust).

---

# 🎯 **Absolute TL;DR Summary**

- **Hot/cold buckets** = best pattern for high-throughput aggregation.
    
- JS **executes code on one thread**, async is fake concurrency.
    
- Event loop queues hold **callbacks**, not results.
    
- `async` alone doesn't make things async; **await does.**
    
- Infinite loops block event loop → async NEVER resumes.
    
- Java async runs on **real threads**, JS async on **event loop**.
    
- Co-routines are the foundation of async across languages.
    

---


---

# 🌟 **SHARDING — The Only Notes You’ll Ever Need**

---

# 1️⃣ **What is Sharding? (Simple Definition)**

**Sharding = splitting data or workload into smaller, independent pieces called “shards”, and processing each shard separately.**

Think of it as:

> “Instead of one giant bucket of work, create 10 small buckets so 10 workers can process in parallel.”

Each shard:

- has its **own data**
    
- its **own processing**
    
- and **no locking or stepping on each other**
    

---

# 2️⃣ **Why Sharding Exists (The Real Reason)**

Because **one machine or one thread cannot handle infinite load.**

When load increases:

- DB becomes slow
    
- single-threaded task gets bottlenecked
    
- event processing gets backed up
    
- counters collide
    
- locks start burning CPU
    

So you break the load into **N fully independent lanes**.

### WITHOUT sharding

```
All requests → One thread → One DB → bottleneck
```

### WITH sharding

```
Request 1 → Shard #1
Request 2 → Shard #7
Request 3 → Shard #3
...
Each shard processed independently.
```

It scales linearly.

---

# 3️⃣ **How Do You Decide Which Shard an Item Goes Into?**

This is the golden question.

### The usual formula:

```
shardId = hash(key) % numberOfShards
```

Where `key` can be:

- userId
    
- profileId
    
- conversationId
    
- emailHash
    
- phoneNumberHash
    

### Example:

32 shards → 32 small in-memory maps.

Profile 12985 goes to:

```
hash(12985) % 32 = 5
→ put into shard5
```

This ensures:

- same user always lands in the same shard
    
- different users get spread out evenly
    

---

# 4️⃣ **Types of Sharding**

### **A. Data Sharding**

Split data itself:

```
User table split by userId ranges:
0–1M   → DB1
1M–2M → DB2
2M–3M → DB3
```

Used by: Twitter, Instagram, Uber, Facebook, WhatsApp.

### **B. Compute Sharding**

Split _processing_:

```
32 executors → each handles a portion of incoming events
```

Used in:

- Kafka consumers
    
- Fraud scoring
    
- Rate limiters
    
- High-throughput counters
    

This is the one **YOU** will use in BloomConnect scoring.

### **C. Sharded caches**

Redis cluster:  
Each node holds part of the keyspace.

---

# 5️⃣ **Why Sharding is OP (Overpowered)**

### ✔️ **1. Removes locking**

If each shard is single-threaded, no more synchronization.

### ✔️ **2. Parallelism**

32 shards = 32x throughput.

### ✔️ **3. Hot/cold bucket + sharding**

Combined = god-tier performance.

### ✔️ **4. Locality**

Same user’s data stays in one place — less coordination, faster reads/writes.

### ✔️ **5. Fault isolation**

Shard 3 is slow?  
Shard 3 only affects shard 3, not the whole system.

---

# 6️⃣ **Sharding in High Throughput Systems**

### Example: scoring incoming events

Say BloomConnect receives **10k events/second**.

Instead of updating one giant map:

```
globalEventsMap.update(profileId)
```

You do:

```
shardId = hash(profileId) % 32
shard[shardId].update(profileId)
```

Each shard:

- has its own **hot bucket**
    
- its own **cold bucket**
    
- its own **flush loop**
    

### Benefits:

- Zero contention
    
- No AtomicInteger overhead
    
- True parallelism
    
- Simpler locking logic
    
- More predictable latency
    

---

# 7️⃣ **Real World Systems Using Sharding**

### **Databases**

- MongoDB
    
- Cassandra
    
- DynamoDB
    
- Elasticsearch
    
- CockroachDB
    

### **Message Systems**

- Kafka (each partition = a shard)
    
- RabbitMQ
    

### **Cache Systems**

- Redis Cluster
    
- Memcached
    

### **Application Logic**

- Twitch: chat sharded by channel
    
- Instagram: feed sharded by userId
    
- Amazon: order events sharded by userId
    
- Uber: surge pricing shards by geographical cell
    

---

# 8️⃣ **What Happens If You _Don’t_ Shard?**

You get:

- bottlenecks
    
- thread contention
    
- DB overload
    
- skew (one user gets too many events)
    
- lock storms
    
- massive latency
    
- everything breaks during peak load
    

Classic “monolithic counter map → dead under load” problem.

---

# 9️⃣ **Sharding + Hot/Cold Buckets = Beast Mode**

**For scoring systems, this combo is undefeated:**

For each shard:

```
hotMap = {}
coldMap = {}

every 10 seconds:
    swap hotMap ↔ coldMap
    process coldMap
    clear coldMap
```

No locks.  
No blocking.  
Massive throughput.  
Shards run in parallel.

---

# 🔟 **Sharding vs Partitioning vs Load Balancing**

### **Sharding**

Split data/work into fixed independent slices.

### **Partitioning**

Often used in DB context — similar meaning.

### **Load balancing**

Distribute traffic randomly or round-robin —  
NOT the same as consistent sharding.

Example:

Load balancer distributes requests.  
Sharding distributes _data ownership_.

---

# 🌟 TL;DR — if you remember only 5 lines:

1. **Sharding = splitting work/data into independent lanes.**
    
2. **Each shard is processed separately — often by a single thread.**
    
3. **Use hash(key) % N to pick a shard.**
    
4. **It eliminates contention and boosts throughput massively.**
    
5. **This is how large systems handle millions of events per second.**
    

---
