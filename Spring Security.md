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

