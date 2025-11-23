
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
    
- **Correlation ID** → _request trace_ identity across microservices
    

---

