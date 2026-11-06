[[API DESIGN]]
API

### Quick comparison

|Technology|Communication|Server → Client|Client → Server|Best for|
|---|---|---|---|---|
|**REST**|Request/response|Only after request|✅|CRUD APIs, normal web/mobile APIs|
|**SSE**|Long-lived HTTP|✅ Continuous|❌ Mainly one-way|AI streaming, notifications, live updates|
|**WebSocket**|Persistent connection|✅ Real-time|✅ Real-time|Chat, games, collaborative apps|
|**gRPC**|RPC over HTTP/2|✅|✅|Microservices, high-performance internal APIs|
|**GraphQL**|Request/response|After request|✅|Flexible data querying|
|**WebRTC**|Peer-to-peer|✅|✅|Video/audio calls, P2P|
|**Long polling**|Repeated HTTP|Simulated|✅|Legacy/simple real-time systems|

### 1. REST

Normal flow:

```text
Client ── GET /users ──> Server
Client <── JSON ───────── Server
```

Example:

```http
GET /api/products/10
```

```json
{
  "id": 10,
  "name": "Laptop",
  "price": 50000
}
```

Use REST for:

- CRUD
    
- Login/register
    
- E-commerce APIs
    
- Mobile apps
    
- Normal frontend ↔ backend communication
    

**Default choice for most Spring Boot APIs.**

---

### 2. SSE — Server-Sent Events

SSE keeps an HTTP connection open:

```text
Client ────────────────> Server
       <── event 1 ─────
       <── event 2 ─────
       <── event 3 ─────
       <── event 4 ─────
```

Server continuously sends data.

Example:

```text
AI response:
"Java"
" is"
" a"
" programming"
" language..."
```

Excellent for:

- **AI response streaming**
    
- Live stock prices
    
- Notifications
    
- Progress updates
    
- Logs
    
- Server status
    

Spring Boot:

```java
@GetMapping(value = "/stream",
            produces = MediaType.TEXT_EVENT_STREAM_VALUE)
public Flux<String> stream() {
    return Flux.just("Hello", "from", "server");
}
```

### Important limitation

SSE is primarily **server → client**.

---

### 3. WebSocket

WebSocket provides two-way real-time communication:

```text
             WebSocket
Client <=================> Server
       messages both ways
```

Example chat:

```text
Alice ── "Hi" ──────────> Server
Bob   <── "Hi" ────────── Server

Bob   ── "Hello" ───────> Server
Alice <── "Hello" ─────── Server
```

Use it for:

- Chat
    
- Multiplayer games
    
- Collaborative editors
    
- Live dashboards
    
- Real-time trading interfaces
    
- Real-time location tracking
    

Unlike SSE:

```text
SSE:
Client ───────> Server     connection
Client <======= Server     data

WebSocket:
Client <=======> Server    data both directions
```

---

### 4. gRPC

Instead of thinking in terms of URLs like REST:

```http
GET /users/10
```

gRPC thinks in terms of calling functions:

```text
getUser(10)
```

It commonly uses **Protocol Buffers** rather than JSON.

```text
Service A
   │
   │ gRPC
   ▼
Service B
```

Very useful for:

- Microservices
    
- Internal service-to-service communication
    
- High-performance systems
    
- Strongly typed APIs
    

For your **Spring Boot microservices**, gRPC is something worth learning after REST.

---

### 5. GraphQL

REST:

```http
GET /users/10
GET /users/10/orders
GET /users/10/profile
```

GraphQL:

```graphql
query {
    user(id: 10) {
        name
        email
        orders {
            id
            total
        }
    }
}
```

Client asks for exactly the data it needs.

Good for:

- Complex frontend applications
    
- Mobile apps
    
- APIs with many related resources
    

---

### 6. WebRTC

WebRTC is different from the others because it is designed for **real-time peer-to-peer communication**.

```text
Browser A <================> Browser B
             WebRTC
          audio/video/data
```

Used for:

- Google Meet-like applications
    
- Video calls
    
- Voice calls
    
- Screen sharing
    
- P2P data
    

Usually a signaling server is still needed to establish the connection.

---

## The most important distinction

Think about **direction of communication**:

```text
REST
Client ─────────> Server
Client <───────── Server
       one request
       one response


SSE
Client ─────────> Server
Client <================ Server
       continuous server updates


WebSocket
Client <================> Server
       continuous two-way communication


gRPC
Client <================> Server
       RPC calls / streaming


WebRTC
Peer A <================> Peer B
       real-time P2P
```

### What should you learn first?

For your **Java + Spring Boot** path, I'd recommend:

```text
1. REST
   ↓
2. HTTP fundamentals
   ↓
3. SSE
   ↓
4. WebSocket
   ↓
5. gRPC
   ↓
6. GraphQL
   ↓
7. WebRTC
```

Especially since you're working with **Spring AI**, learn **SSE very well**. It's particularly useful for streaming an LLM response from your Spring Boot backend to a frontend.

A practical architecture could be:

```text
React
  │
  │ REST
  ▼
Spring Boot
  │
  │ SSE
  ▼
React ←── streamed AI response
  │
  ▼
User
```

For **chat with real-time messages**, switch the SSE part to **WebSocket**.