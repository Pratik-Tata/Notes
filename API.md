
# 📚 API Types Cheat Sheet

> With clear examples, use cases, and what's best for what

---

## 1️⃣ 🔁 **REST API (REpresentational State Transfer)**

**Most common. Used everywhere.**

- **Request/Response model** — client asks, server answers.
    
- Stateless (each request is independent)
    
- Works over HTTP(S)
    
- Typically uses JSON
    

### 📦 Example:

```http
GET https://api.weather.com/v1/current?city=mumbai
```

### ✅ Good for:

- Basic data fetching
    
- CRUD operations
    
- Simplicity, caching
    

### ❌ Not good for:

- Real-time updates (you need to keep polling)
    

---

## 2️⃣ ⚡ **Streaming API**

A **subset of APIs** designed for real-time, continuous data delivery.

There are two flavors:

### a) 🔌 **WebSockets**

- Full-duplex (client & server talk anytime)
    
- Always open connection
    
- Used for **stock tickers**, **chat apps**, **live dashboards**
    

#### ✅ Example:

- Zerodha WebSocket feed
    
- Binance live ticker
    
- TradingView real-time chart
    

---

### b) 🚿 **Server-Sent Events (SSE)**

- One-way push from **server to client**
    
- Simpler than WebSockets
    
- Good for notifications, news feeds
    

---

## 3️⃣ 🔍 **GraphQL API**

- Query-based API — **ask exactly for what you need**.
    
- One endpoint for all requests.
    
- You define shape of response.
    

### 📦 Example:

```graphql
query {
  user(id: 1) {
    name
    email
    posts {
      title
    }
  }
}
```

### ✅ Great for:

- Mobile apps (get just the data you need)
    
- Avoiding over-fetching/under-fetching
    

### ❌ Can be:

- Overkill for small apps
    
- Complex to cache/debug
    

---

## 4️⃣ 🔄 **gRPC / RPC (Remote Procedure Call)**

- Binary protocol (uses Protocol Buffers)
    
- Extremely fast and efficient
    
- Like calling a method on a remote object
    

### 📦 Example:

- Microservices talking to each other
    
- Backend-to-backend communication
    

✅ Used by:

- Google, Netflix, high-perf systems
    

❌ Not browser-friendly (needs special clients)

---

## 5️⃣ 📡 **Graph Streaming / PubSub APIs (MQTT, Kafka)**

- Not technically “web APIs” but still APIs.
    
- Data is **published and subscribed** to in real-time.
    
- Used in IoT, telemetry, real-time data lakes.
    

---

## 🧠 TL;DR Comparison Table

|API Type|Real-Time|Stateless|Best For|
|---|---|---|---|
|REST|❌|✅|Simplicity, CRUD, wide support|
|WebSocket|✅|❌|Live data, chats, tickers|
|SSE|✅ (one-way)|✅|Notifications, feed updates|
|GraphQL|❌ (but supports subscriptions)|✅|Flexible data queries|
|gRPC|❌|✅|Backend-to-backend, high perf|
|Streaming API|✅|❌/✅|Real-time financial/IoT/data|

---

## 📈 Real-World Use Cases:

|Use Case|API Type|
|---|---|
|Stock charts|WebSocket or Streaming API|
|Login, signup|REST|
|Chat app|WebSocket|
|Analytics dashboard|REST + WebSocket|
|Microservices|gRPC or REST|
|IoT sensors|MQTT or Streaming APIs|
