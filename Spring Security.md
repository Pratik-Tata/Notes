**OAuth 2.0, OpenID Connect, and SSO: Master-Level Study Guide**

---

# 🌐 Core Concepts

### ✅ **OAuth 2.0**

- **Purpose**: Delegated authorization
    
- **Entities**:
    
    - _Resource Owner_: The user
        
    - _Client_: App requesting access
        
    - _Resource Server_: Hosts protected resources (e.g. APIs)
        
    - _Authorization Server_: Issues access tokens (e.g. Google, Auth0)
        
- **Grant Types**:
    
    - Authorization Code (most secure)
        
    - Client Credentials
        
    - Resource Owner Password Credentials (deprecated)
        
    - Implicit (deprecated)
        
- **Tokens**:
    
    - Access Token (short-lived)
        
    - Refresh Token (to get new access tokens)
        

### 🔎 **OpenID Connect (OIDC)**

- **Built on top of OAuth 2.0**
    
- **Purpose**: Authentication layer
    
- **Adds**:
    
    - ID Token (JWT) with user identity claims
        
    - `userinfo` endpoint
        
- **Use Case**: SSO (Single Sign-On), getting user info, login delegation
    

### 🤝 **Single Sign-On (SSO)**

- **Achieved with OIDC**
    
- User authenticates once with IDP (e.g. Google)
    
- Token can be used across services
    
- Typically stateless when using JWT-based tokens
    

---

# 🔒 Tokens & Claims

### 💰 **Access Token**

- Grants access to resource server
    
- Contains scopes and expiry info
    

### � **ID Token**

- JWT returned by OIDC
    
- Contains claims: `sub`, `email`, `name`, `iat`, `exp`
    

### 🏛 **Claims**

- Key-value pairs embedded in tokens
    
- Can be standard or custom
    

### ❗ **Token Validation**

- Signature (with JWK)
    
- Expiry check
    
- Audience (`aud`) and issuer (`iss`) match
    

---

# 🚫 OAuth vs OIDC

|Feature|OAuth 2.0|OpenID Connect|
|---|---|---|
|Purpose|Authorization|Authentication|
|Returns ID Token|No|Yes|
|User Info Endpoint|No|Yes|
|Built on OAuth?|N/A|Yes|
|Use Case|API Access|Login & Identity|

---

# 🌟 OAuth Flow (Authorization Code)

1. Client redirects to Authorization Server (with client_id, redirect_uri, etc.)
    
2. User logs in and consents
    
3. Auth server redirects back with `code`
    
4. Client exchanges `code` for access & ID token
    
5. Client uses access token to call Resource Server
    

---

# 📄 Token Storage & Security

- Store tokens in HttpOnly cookies or secure storage
    
- Do NOT store in localStorage (XSS risk)
    
- Use `state` and `nonce` parameters to prevent CSRF/replay attacks
    
- Validate token signature & expiry before trusting
    

---

# 🚀 Spring Boot Integration

### ⚙️ Resource Server:

- Add Spring Security dependency
    
- Configure `OAuth2ResourceServerConfigurer`
    
- Validate JWT automatically
    

### ⚖️ Authorization Server:

- Use Spring Authorization Server project
    
- Issue signed JWTs
    

### 🌎 External IDP:

- Configure OIDC client with `spring.security.oauth2.client.registration`
    
- Redirect user to Google/Auth0, etc.
    
- Spring handles callback and security context
    

---

# 🤔 FAQs

### Q: Can OAuth 2.0 alone identify a user?

> No. It’s for authorization. Use OIDC for identity claims.

### Q: Can I be stateless with OAuth?

> Yes. Use JWT tokens and don’t store sessions server-side.

### Q: Does Spring create a session even in OAuth?

> Not unless explicitly configured. Stateless is default for resource servers.

---

# 🧠 Authentication & Authorization in Microservices with Kafka

### (Why JWT stops at the edge and events don’t carry tokens)

---

## 1️⃣ Two very different worlds: HTTP vs Kafka

Before anything else, this distinction is **non-negotiable**.

### 🌐 HTTP (Synchronous, Request–Response)

- Short-lived
    
- User-driven
    
- Point-to-point
    
- Immediate authorization decision
    

👉 JWT fits **perfectly** here.

### 📦 Kafka (Asynchronous, Event-driven)

- Long-lived messages
    
- Replayable
    
- Fan-out (one → many)
    
- No immediate “user request”
    

👉 JWT fits **poorly** here.

Trying to treat Kafka like HTTP is the root cause of most confusion.

---

## 2️⃣ What JWT is actually meant for

A JWT is a **signed assertion** that says:

> “At time T, the issuer asserts that subject X has roles/scopes Y, and this assertion expires at time E.”

JWTs are designed to:

- Be verified **per request**
    
- Be short-lived
    
- Be audience-specific
    
- Be stateless
    

They are **not** designed to:

- Live in queues
    
- Be replayed days later
    
- Be fanned out to many consumers
    

---

## 3️⃣ The correct place for JWT in microservices

### ✅ JWT belongs at **command boundaries**

Example:

```
User
  ↓ (JWT)
API Gateway / Edge Service
  ↓
Business Service (HTTP)
```

At this boundary, the system must answer:

> “Is this user allowed to ask for this action _right now_?”

So here we:

- Verify JWT signature
    
- Check expiry
    
- Check issuer & audience
    
- Authorize using roles/scopes
    

Once the decision is made, **JWT has done its job**.

---

## 4️⃣ What happens after authorization succeeds

Once a service authorizes a request, it turns that **request** into a **fact**.

Example:

```
User (ADMIN) requests: "Approve Order #123"
↓
Service A authorizes request
↓
Service A emits event:
"ORDER_APPROVED"
```

This event means:

> “The system has already decided this is allowed.”

Events are **not asking for permission**.  
They are **statements of truth**.

---

## 5️⃣ Why JWT must NOT be put into Kafka events

Putting JWTs into Kafka events causes multiple hard problems:

### ❌ Token expiry vs message lifetime

- JWT: minutes
    
- Kafka messages: hours, days, weeks
    

A perfectly valid event could become “unauthorized” later — nonsense.

---

### ❌ Replay breaks auth semantics

Kafka supports:

- Replays
    
- Backfills
    
- DLQs
    
- Reprocessing
    

Auth tokens are time-bound.  
Events are time-independent.

---

### ❌ Audience (`aud`) mismatch

JWTs are issued for:

- Specific services
    
- Specific clients
    

Kafka events:

- Go to many consumers
    
- Often unknown at publish time
    

Audience semantics collapse.

---

### ❌ Security blast radius

JWT in Kafka means:

- Stored on disk
    
- Replicated
    
- Backed up
    
- Readable by many services
    

You just widened your attack surface dramatically.

---

## 6️⃣ So how do downstream services “know who did it”?

They don’t re-authenticate the user.

They rely on **event context**, not credentials.

### Typical event structure:

```json
{
  "eventType": "ORDER_APPROVED",
  "orderId": "123",
  "actor": {
    "type": "USER",
    "id": "user-42",
    "roles": ["ADMIN"]
  },
  "timestamp": "2026-01-08T10:15:00Z",
  "correlationId": "abc-123"
}
```

Key points:

- No JWT
    
- No secrets
    
- No signatures required
    
- Just **identity context for audit and logic**
    

---

## 7️⃣ The real trust model in Kafka-based systems

### 🔐 Kafka authenticates **services**, not users

Services authenticate to Kafka using:

- mTLS
    
- SASL/OAUTH
    
- IAM / workload identity
    

Kafka guarantees:

> “This message came from Service A.”

That becomes the **security anchor**.

---

## 8️⃣ What downstream services actually trust

Downstream services trust:

1. **Kafka** (transport security)
    
2. **Producer identity** (Service A)
    
3. **Event contract** (schema + semantics)
    

They do **not** trust:

- User tokens
    
- Runtime user authentication
    
- Expired credentials
    

Instead, they trust that:

> “If Service A emitted this event, Service A already did the right authorization.”

This is called **bounded-context trust**.

---

## 9️⃣ Where authorization really lives (important)

|Layer|Responsibility|
|---|---|
|Edge / HTTP services|Authenticate users (JWT)|
|Command handlers|Authorize actions|
|Kafka events|Represent decisions|
|Consumers|Enforce business invariants, not auth|

If a consumer re-checks user permissions:

- Your boundaries are wrong
    
- Your services are too coupled
    
- Your system is fragile
    

---

## 🔁 Mental shift that unlocks everything

> **Authentication answers “who are you _right now_?”  
> Events answer “what already happened?”**

You don’t authenticate history.

---

## 10️⃣ Rare exceptions (know they exist)

Including JWT in Kafka may make sense **only if**:

- No replay
    
- Single consumer
    
- Short retention
    
- Strong encryption
    
- Explicit security review
    

These are **edge cases**, not patterns.

---

## 11️⃣ Final summary (bookmark this)

- JWT is for **synchronous authorization**
    
- Kafka carries **facts, not credentials**
    
- Services authenticate to Kafka, not users
    
- Authorization happens **before** events are emitted
    
- Downstream services trust producer identity + event contract
    
- Forwarding JWT through Kafka is usually a design smell
    

---

## 🧠 One-line principle (the money line)

> **Commands need authentication. Events represent authorized outcomes.**

If you internalize that, distributed security suddenly feels… calm.

---

# 🔐 “Kafka authenticates services, not users” — explained properly

## First: what Kafka actually _is_ in security terms

Kafka is **not**:

- an application layer
    
- a business API
    
- a user-facing system
    

Kafka **is infrastructure**.

So Kafka does **not care** about:

- users
    
- roles
    
- JWTs
    
- permissions like “ADMIN”
    

Kafka only answers one question:

> **“Which service is connecting to me?”**

That’s it.

---

## 1️⃣ What does “authenticate a service” actually mean?

It means Kafka can cryptographically prove:

> “The producer or consumer on this connection is Service A (and not someone pretending).”

This proof happens **before any message is sent or read**.

Once authenticated, Kafka applies **ACLs**:

- Can Service A:
    
    - write to topic X?
        
    - read from topic Y?
        
    - create topics?
        
    - use this consumer group?
        

This is **infrastructure-level authorization**, not business authorization.

---

## 2️⃣ How Kafka proves “this is Service A”

Kafka does **not trust headers or payloads**.  
It trusts **the connection itself**.

Let’s go through the main mechanisms.

---

## 3️⃣ mTLS (Mutual TLS) — the clearest model

### What happens

1. Service A connects to Kafka broker
    
2. Kafka presents its certificate
    
3. Service A presents _its_ certificate
    
4. Kafka verifies:
    
    - certificate is valid
        
    - issued by trusted CA
        
    - subject = `service-a`
        

At that moment Kafka knows:

> “This TCP connection is cryptographically bound to Service A.”

No token. No payload inspection.  
Identity is tied to the **socket**.

### Result

Kafka can enforce:

```text
Service A → can write to topic orders-approved
Service B → cannot
```

This is extremely strong security.

---

## 4️⃣ SASL/OAUTH — service identity via tokens

This is often confused with user OAuth, but it’s **not the same thing**.

### Flow (simplified)

1. Service A obtains a **service token**
    
    - via IAM
        
    - via workload identity
        
    - via OAuth client credentials
        
2. Service A connects to Kafka using SASL/OAUTH
    
3. Kafka validates the token
    
4. Kafka extracts:
    
    ```
    principal = service-a
    ```
    

Again, Kafka learns:

> “This connection belongs to Service A.”

This token is **never forwarded to other services**.  
It exists only to authenticate **to Kafka itself**.

---

## 5️⃣ IAM / Workload Identity (cloud-native version)

In cloud platforms:

- Pod / VM has a workload identity
    
- Kafka trusts the cloud IAM
    
- No secrets in config
    
- No certificates to rotate manually
    

Effectively:

> “If this pod is running as Service A’s identity, Kafka accepts it as Service A.”

Still the same model:

- Kafka authenticates **service identity**
    
- Not user identity
    

---

## 6️⃣ Why this identity becomes the **security anchor**

Now the key insight:

> Kafka **guarantees the origin of a message**, not its intent.

Once Kafka says:

> “This message was produced by Service A”

That fact is:

- cryptographically enforced
    
- non-forgeable
    
- infrastructure-backed
    

Downstream services can safely assume:

> “Only Service A could have produced this event.”

That is **much stronger** than trusting a JWT inside a payload.

---

## 7️⃣ Why Kafka does NOT care about users

Let’s be blunt:

Kafka has no idea:

- which HTTP request caused the event
    
- which user clicked a button
    
- whether a token was expired
    

And it **should not**.

Because Kafka messages:

- may be produced asynchronously
    
- may be retried
    
- may be replayed
    
- may outlive user sessions
    

User authentication is **time-bound**.  
Kafka messages are **time-independent**.

Mixing them is a category error.

---

## 8️⃣ What downstream services really trust

When Service B consumes a message, it trusts:

1. **Kafka’s authentication**
    
    - “This came from Service A”
        
2. **Topic-level ACLs**
    
    - “Only Service A can write here”
        
3. **Event schema**
    
    - “This event means X by contract”
        

So when Service B sees:

```json
{
  "eventType": "ORDER_APPROVED",
  "actor": { "id": "user-42", "roles": ["ADMIN"] }
}
```

It interprets it as:

> “Service A asserts that user-42 (ADMIN) approved the order.”

Service B does **not** ask:

- “Was the JWT valid?”
    
- “Is the role still active?”
    
- “Is the token expired?”
    

Because that decision already happened.

---

## 9️⃣ Why this model is safer than forwarding JWTs

Because the trust boundary is **clear and narrow**:

|What is trusted|Why|
|---|---|
|Kafka|Infrastructure security|
|Producer service|Strong service identity|
|Event contract|Explicit semantics|

Instead of:

- trusting tokens
    
- trusting timestamps
    
- trusting replayed credentials
    

---

## 🔑 The sentence to lock in (this is the core)

> **Kafka authenticates _who produced the message_, not _who the user was_.  
> That producer identity becomes the root of trust for all downstream consumers.**

Once you accept that, everything else falls into place.

---

---

## 🧱 1. Layers of validation (core architecture)

### ✅ Controller

👉 Structural validation

Examples:

- null
    
- empty
    
- format
    
- length
    

Tools:

- DTO + `@Valid`
    

Purpose:  
➡️ “Is request well-formed?”

---

### ✅ Domain

👉 Invariants (always true)

Examples:

- age > 18
    
- new name != old name
    
- no negative counters
    

Lives in:

- constructors
    
- domain methods
    

Purpose:  
➡️ “Can this object exist in this state?”

---

### ✅ Service

👉 Business/context rules

Examples:

- email unique
    
- date not future
    
- user allowed now
    

Depends on:

- DB
    
- time
    
- workflows
    

Purpose:  
➡️ “Is operation allowed now?”

---

---

# 🔐 2. Spring Security structure

### Location:

```
com.bloomconnect.security (or config)
```

Security = infrastructure, not domain.

---

## 🛡️ 3. Security layers

### SecurityFilterChain (coarse)

Controls:

- public vs protected URLs
    
- broad role groups
    

Example:

```java
.requestMatchers("/admin/**").hasRole("ADMIN")
```

---

### @PreAuthorize (fine-grained)

Controls:

- business operations
    
- ownership
    
- precise permissions
    

Example:

```java
@PreAuthorize("hasRole('USER')")
```

---

---

# 🌍 4. IAM mental model (universal)

|Concept|Keycloak|Azure|GCP|
|---|---|---|---|
|Tenant|Realm|Directory|Org|
|App|Client|App registration|OAuth client|
|User|User|User|User|
|Roles|Realm roles|App roles|IAM roles|
|Token|JWT|JWT|JWT|

Flow everywhere:

User → IAM → JWT → Backend → Authorization

---

# 🧾 5. JWT + OIDC basics

JWT contains:

### Standard claims:

- sub (user id)
    
- iss (issuer)
    
- exp
    
- aud
    
- name/email (optional)
    

### Custom claims:

- roles
    
- metadata
    
- tier
    
- org
    

OIDC defines identity claims.

Authorization claims (roles) are provider-specific.

---

---

# 🔄 6. Spring Security JWT flow

1️⃣ Request comes with JWT  
2️⃣ Spring validates signature & expiry  
3️⃣ JWT converted to Authentication  
4️⃣ Authorities extracted  
5️⃣ SecurityContext populated

---

# 👤 7. Authentication object structure

```
Authentication
 ├── principal   -> user identity (JWT by default)
 ├── authorities -> roles/permissions
 └── credentials -> unused for JWT
```

---

# 🔧 8. Why JwtAuthenticationConverter exists

Spring doesn’t know where IAM puts roles.

Each provider differs:

|Provider|Role location|
|---|---|
|Keycloak|realm_access.roles|
|Azure|roles|
|Auth0|permissions|

So we tell Spring:

👉 “Extract roles from here.”

---

# ✅ 9. Keycloak Realm role mapping code

```java
@Bean
JwtAuthenticationConverter jwtAuthenticationConverter() {

    JwtAuthenticationConverter converter = new JwtAuthenticationConverter();

    converter.setJwtGrantedAuthoritiesConverter(jwt -> {

        Map<String, Object> realmAccess = jwt.getClaim("realm_access");
        if (realmAccess == null) return List.of();

        List<?> roles = (List<?>) realmAccess.get("roles");
        if (roles == null) return List.of();

        return roles.stream()
                .map(Object::toString)
                .map(role -> (GrantedAuthority) 
                     new SimpleGrantedAuthority("ROLE_" + role))
                .collect(Collectors.toList());
    });

    return converter;
}
```

---

# 🔌 10. Wiring JWT in Spring Boot

### application.yml

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:8080/realms/bloomconnect
```

---

### SecurityConfig

```java
.oauth2ResourceServer(oauth ->
    oauth.jwt(jwt -> 
        jwt.jwtAuthenticationConverter(jwtAuthenticationConverter())
    )
)
```

---

# 🎯 11. Where user info lives

- Roles → `authentication.getAuthorities()`
    
- Identity data → `authentication.getPrincipal()` (JWT)
    

Example:

```java
Jwt jwt = (Jwt) auth.getPrincipal();
jwt.getSubject();
jwt.getClaim("email");
```

---

# 🧩 12. Custom principal (optional later)

You can map JWT → custom user object for cleaner ownership logic.

Roles still live in authorities.

---

# 🏰 13. Realm roles vs Client roles

### Realm roles:

- global
    
- simple
    
- great for most apps
    

Stored in:

```
realm_access.roles
```

---

### Client roles:

- app-specific
    
- fine-grained
    

Stored in something like:

```
resource_access[client-id].roles
```

👉 Mapping code is basically same, just different claim path.

Your instinct was correct 👍

---

# ⚠️ 14. Keycloak dev mode notes

- temp admin user = normal
    
- dev DB may reset
    
- required actions block token issuing
    

---

# 📌 15. Password grant

Used for dev/testing.

In real apps:

👉 React uses Authorization Code Flow.

---

## ✅ Big picture stack you now know

✔ Spring Security  
✔ JWT  
✔ OAuth2  
✔ OpenID Connect  
✔ Role-based auth  
✔ Enterprise IAM model

---

# 📚 Spring Security + JWT — Authentication, Principal & Converters (Complete Notes)

---

## 🔐 1. What happens when a JWT hits your backend?

High level pipeline:

```
Request
 → JWT extracted from header
 → JWT signature validated
 → JWT parsed into Jwt object
 → Authentication created
 → Stored in SecurityContext
```

From this point on:

👉 Your app no longer works with “token”  
👉 It works with `Authentication`

---

## 🧩 2. What is Authentication?

Think of it as a container with 3 things:

```
Authentication
 ├─ principal     → WHO the user is
 ├─ credentials   → PROOF (password/token)
 └─ authorities   → WHAT they can access
```

### Important:

There is only ONE principal.

When people say:

> authenticated principal

They literally mean:

👉 `authentication.getPrincipal()`

Same thing.

---

## 📦 3. Why does getPrincipal() return Object?

Because Spring is generic.

Principal can be:

• Jwt  
• UserDetails  
• String  
• Custom object

Spring doesn’t care — you decide.

So method returns `Object`.

You cast based on setup.

---

## ✅ 4. Default JWT behavior (OAuth2 Resource Server)

If you use:

```
oauth2ResourceServer().jwt()
```

(Spring’s modern way)

Then automatically:

```
principal   = Jwt object
credentials = Jwt (or ignored)
authorities = extracted from claims
```

👉 NO setup required  
👉 This is default

So:

```
authentication.getPrincipal() → Jwt
```

---

### What’s inside Jwt?

Not just token string.

It has:

• headers  
• claims (sub, roles, email, exp, etc)  
• timestamps  
• raw token value

JWT becomes your **identity container**.

---

## ❗ 5. Does Spring ever automatically create UserDetails from JWT?

👉 NO.

Never by default.

If you see:

```
(UserDetails) authentication.getPrincipal()
```

That ONLY works if:

✅ you explicitly mapped JWT → UserDetails

Spring won’t do it magically.

---

## 🔄 6. What is JwtAuthenticationConverter?

Despite the big name 😅 it mainly exists to:

👉 customize authorities (roles/scopes)

Spring internally does:

```
Jwt → JwtAuthenticationToken
```

and exposes hook only for:

✔ authorities mapping

NOT for:

❌ principal  
❌ credentials

By design.

---

### Why?

Because Spring assumes:

> “Jwt itself already represents identity + proof”

So they locked:

principal = Jwt

and let you only tweak roles.

---

## 🧠 7. So how do we customize principal?

There are TWO approaches:

---

### ✅ Approach A (modern & recommended)

Keep:

```
principal = Jwt
```

Just read claims wherever needed.

Example conceptually:

```
userId = jwt.sub
email  = jwt.claim("email")
```

✔ fast  
✔ stateless  
✔ scalable

This is what most microservices do.

---

### ✅ Approach B (full custom mapping)

You replace default behavior with your own converter:

```
Jwt → Authentication (you build it)
```

Inside you:

• extract username/userId from JWT  
• optionally load user from DB  
• set:

```
principal = UserDetails (or custom object)
credentials = null
authorities = roles
```

Now:

```
getPrincipal() → UserDetails
```

But:

⚠️ DB lookup on every request  
⚠️ hurts scalability

(acceptable for small systems)

---

## 🔐 8. What about credentials in JWT auth?

You were spot on 👍

In JWT world:

👉 credentials are basically useless after verification

Best practice:

```
credentials = null
```

or left internally as token.

Because:

• no password  
• token already verified

JWT = proof + identity combined.

---

## 📊 9. Quick truth table

|Setup|Principal|Who sets it|Typical use|
|---|---|---|---|
|Default resource server|Jwt|Spring|Modern APIs|
|JwtAuthenticationConverter|Jwt|Spring|Only role mapping|
|Custom converter|UserDetails/custom|You|Legacy / DB-based|
|Custom filter|Anything|You|DIY setups|

---

## 🧠 10. The BIG mental model (remember this)

JWT is just a proof sent by client.

Spring turns it into:

👉 Authentication object

From then on:

Your system talks to **principal**, not token.

Sometimes principal is:

🪪 Jwt (claims container)  
🪪 UserDetails (DB-backed user)

Both are valid designs.

---

## 🎯 11. Why Spring prefers Jwt as principal by default

Because:

✅ JWT already contains identity  
✅ avoids DB calls  
✅ truly stateless  
✅ scales better

UserDetails mapping is more “old Spring session era” thinking.

---

## 🧩 Final super-simple summary

👉 `getPrincipal()` = authenticated user representation  
👉 Default JWT setup → principal is Jwt  
👉 UserDetails only appears if YOU map it  
👉 JwtAuthenticationConverter mainly customizes authorities  
👉 For full control → custom converter  
👉 Credentials usually ignored in JWT

---

If you internalize just this one line, you’re golden:

> Spring turns JWT into Authentication. By default the JWT itself becomes the principal. Anything else requires explicit mapping.

---

# 🔐 OAuth2 / OIDC Core Actors (quick base)

• **Client** → your app (frontend, backend, mobile)  
• **Authorization Server** → Keycloak, Google, etc  
• **Resource Server** → backend API  
• **User (resource owner)**

Goal:  
👉 user authenticates once  
👉 client gets token  
👉 token accesses backend safely

---

# 🔄 Standard Flow (Authorization Code Flow)

### What happens:

1. Client redirects user to auth server login page
    
2. User logs in there
    
3. Auth server redirects back with **authorization code**
    
4. Client exchanges code with auth server for **tokens**
    

### Important:

• browser only sees short-lived code  
• token is obtained server-to-server (secure)

### Why it exists:

✅ prevents token leaks in browser  
✅ client never sees user password  
✅ supports SSO

### Used by:

• Google login  
• enterprise SSO  
• modern web/mobile apps

### Modern best practice.

---

# 🔑 PKCE (Proof Key for Code Exchange)

Used with Standard Flow mainly for **public clients** (SPAs, mobile).

### Idea:

Since public apps can’t store client_secret safely:

1. client creates random secret (verifier)
    
2. sends hashed version (challenge) in auth request
    
3. later proves it during token exchange
    

### Why:

✅ prevents stolen auth code attacks

### Recommended:

👉 PKCE with S256

---

# 🚫 Implicit Flow (Deprecated)

### What happens:

1. redirect to auth server login
    
2. user logs in
    
3. auth server redirects back with **token directly in URL**
    

### Problems:

❌ token exposed in browser  
❌ stored in history/logs  
❌ easy to steal

### Status:

⚠️ Deprecated. Do not use.

---

# 🚫 Direct Access Grant (Resource Owner Password Grant)

### What happens:

1. client shows its own login form
    
2. user enters username/password
    
3. client sends creds directly to auth server
    
4. auth server returns token
    

### Why it exists:

• legacy apps  
• scripts/CLI  
• trusted internal systems

### Problems:

❌ app handles passwords  
❌ breaks OAuth trust separation  
❌ higher risk

### Status:

⚠️ discouraged but still supported

---

# 🔐 Client Authentication (ON/OFF)

### OFF → Public Client

• no client secret  
• used by frontend/mobile apps

Security relies on:

👉 PKCE

---

### ON → Confidential Client

• client must authenticate itself  
• uses client_secret or cert

Used by:

• backend services  
• server-side apps  
• machine-to-machine

Adds extra security layer:

👉 “Who is allowed to ask for tokens?”

---

# 🧠 Authorization Services (Keycloak specific)

Advanced permission system.

Instead of just:

• roles  
• scopes

You can define:

• resources  
• policies  
• permissions

Example:

“User can view invoice only if owner and during business hours”

Used in complex enterprise apps.

Most apps keep it OFF.

---

# 🔁 Token Exchange

Allows swapping one token for another.

Example:

• service A has token  
• exchanges for token to call service B

Used in:

• microservices  
• federation

Advanced use case.

---

# 📱 OAuth2 Device Grant

For devices without browser/keyboard.

Flow:

• device shows code  
• user logs in on phone/computer  
• device gets token

Used in:

Smart TVs, consoles, IoT.

---

# 📲 OIDC CIBA Grant

Backchannel authentication.

Flow:

• login triggered  
• user gets push notification  
• approves  
• token issued

Used in:

Banking, high-security environments.

---

# 🔐 DPoP (Proof of Possession Tokens)

Instead of normal bearer token:

👉 token is cryptographically bound to client

So:

Even if stolen → attacker can’t use it.

Very secure.  
New standard.  
Rare in practice (yet).

---

# 🎯 Why redirect-based auth is king

Because it ensures:

✅ passwords only go to auth server  
✅ client never touches credentials  
✅ tokens exchanged securely

Never proxy auth through backend.

---

# 📊 Clean flow comparison

|Flow|Redirect Login|App sees password|Token via browser|Recommended|
|---|---|---|---|---|
|Standard|✅|❌|❌|✅|
|Implicit|✅|❌|✅|❌|
|Direct Access|❌|✅|❌|⚠️|

---

# 🧩 How frontend + backend usually works

1. Frontend uses Standard Flow (+ PKCE)
    
2. Gets access token
    
3. Sends token to backend API
    
4. Backend validates token (resource server)
    

Backend NEVER handles login UI.

---

# 🏁 One master mental model

OAuth splits responsibility:

Auth Server → authentication + token issuing  
Client → uses token  
Resource Server → validates token

Never mix these roles.

---

