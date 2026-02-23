# OWASP API Security Top 10 (2023) — Scan Reference

For each category: search every listed pattern, then run the semantic queries.

---

## API1: Broken Object Level Authorization (BOLA)

API endpoints expose object IDs that allow attackers to access other users' data by manipulating IDs.

### Grep Patterns

```
pattern: "(?i)(params|query)\.(id|userId|user_id|orderId|order_id|accountId|account_id)"
  why: Object ID taken directly from request parameters

pattern: "(?i)findById\(|findOne\(\{.*_id|findUnique\(\{.*id"
  why: Database lookup by ID — verify authorization check exists

pattern: "(?i)DELETE.*/:id|PUT.*/:id|PATCH.*/:id"
  why: Mutation endpoints with ID parameters — must check ownership

pattern: "(?i)where:\s*\{.*id:\s*(req\.|params\.|args\.)"
  why: ORM query using unvalidated user-supplied ID
```

### Semantic Queries

- "Where are database records fetched by ID from request parameters without verifying the requesting user owns the record?"
- "Are there API endpoints where changing an object ID in the URL allows access to another user's data?"

### CWE IDs

CWE-284, CWE-285, CWE-639

### Remediation

Always verify the authenticated user has permission to access the requested object. Implement ownership checks at the data layer. Use UUIDs instead of sequential IDs to reduce enumeration risk (but still enforce authorization).

---

## API2: Broken Authentication

Weak or missing authentication mechanisms on API endpoints.

### Grep Patterns

```
pattern: "(?i)(app\.(get|post|put|delete|patch|all))\s*\([^,]+,\s*(async\s*)?\("
  why: Route handler without middleware — check if auth middleware is missing

pattern: "(?i)public.*route|noAuth|skipAuth|isPublic|allowAnonymous"
  why: Intentionally unprotected routes — verify they should be public

pattern: "(?i)Bearer|Authorization|x-api-key"
  why: Verify token validation is actually performed, not just extracted

pattern: "(?i)api.?key\s*===?\s*req\."
  why: Simple API key comparison — check for timing-safe comparison
```

### Semantic Queries

- "Which API endpoints lack authentication middleware?"
- "How are API tokens validated — is the signature, expiration, and issuer verified?"
- "Are there endpoints that should require authentication but are currently public?"

### CWE IDs

CWE-287, CWE-306, CWE-307, CWE-798

### Remediation

Apply authentication middleware to all non-public endpoints. Use timing-safe comparison for tokens/keys (`crypto.timingSafeEqual`). Validate JWT signature, expiration, audience, and issuer. Implement rate limiting on authentication endpoints.

---

## API3: Broken Object Property Level Authorization

API returns more data than the client needs, or accepts properties it should not.

### Grep Patterns

```
pattern: "(?i)res\.(json|send)\s*\(\s*(user|account|profile|record)"
  why: Entire object returned — may contain sensitive fields (password hash, internal IDs)

pattern: "(?i)\.\.\.(req\.body|body|input|args)"
  why: Mass assignment — spreading request body into database operations

pattern: "(?i)(select|project)\s*\(\s*\)"
  why: Selecting all fields without filtering

pattern: "(?i)\.toJSON\(\)|\.toObject\(\)"
  why: Full model serialization — check for sensitive field exclusion

pattern: "(?i)Object\.assign\(.*,\s*req\.body"
  why: Mass assignment via Object.assign
```

### Semantic Queries

- "Are API responses exposing internal fields like password hashes, internal IDs, or role assignments?"
- "Can users update fields they should not have access to (e.g., role, isAdmin, balance)?"
- "Are database query results filtered before being sent to the client?"

### CWE IDs

CWE-213, CWE-915

### Remediation

Explicitly select/allowlist fields returned in API responses. Never spread request body directly into database operations — use an allowlist of permitted fields. Define DTOs or response schemas that exclude sensitive properties.

---

## API4: Unrestricted Resource Consumption

Missing rate limiting, pagination limits, or resource constraints.

### Grep Patterns

```
pattern: "(?i)(rate.?limit|throttle|express-rate-limit|bottleneck)"
  why: Check if rate limiting is implemented (absence is a finding)

pattern: "(?i)(limit|pageSize|per_page|take)\s*[:=]\s*(req\.|params\.|query\.)"
  why: Client-controlled pagination limit — verify maximum is enforced

pattern: "(?i)upload.*(size|limit|max)"
  why: Check if file upload size limits are configured

pattern: "(?i)timeout|maxBodyLength|maxContentLength"
  why: Check if request timeouts and body size limits exist
```

### Semantic Queries

- "Is rate limiting applied to API endpoints, especially authentication and data-heavy operations?"
- "Can clients request unlimited amounts of data (missing pagination limits)?"
- "Are file upload size limits enforced?"
- "Are request body size limits configured?"

### CWE IDs

CWE-770, CWE-799, CWE-400

### Remediation

Implement rate limiting on all endpoints (stricter on auth endpoints). Enforce maximum pagination limits server-side regardless of client request. Set request body size limits. Set timeouts on all external calls. Limit file upload sizes.

---

## API5: Broken Function Level Authorization

Users access administrative or privileged API functions they should not.

### Grep Patterns

```
pattern: "(?i)/admin|/internal|/management|/backoffice|/debug"
  why: Administrative endpoints — verify authorization is enforced

pattern: "(?i)(isAdmin|role\s*===?\s*['\"]admin|hasPermission|authorize)"
  why: Authorization checks — verify they are applied consistently

pattern: "(?i)app\.(get|post|put|delete)\s*\(\s*['\"]/(admin|internal|debug)"
  why: Admin routes — verify middleware protection
```

### Semantic Queries

- "Are administrative API endpoints protected by role-based authorization?"
- "Can regular users access admin functionality by calling admin endpoints directly?"
- "Is there consistent authorization middleware on privileged routes?"

### CWE IDs

CWE-285

### Remediation

Enforce role-based access control (RBAC) on all administrative endpoints. Apply authorization middleware at the router level, not within individual handlers. Deny by default — require explicit permission grants. Separate admin routes into a distinct router with blanket auth middleware.

---

## API6: Unrestricted Access to Sensitive Business Flows

APIs that expose business-critical flows (purchasing, posting, booking) without bot/abuse protection.

### Grep Patterns

```
pattern: "(?i)(purchase|checkout|order|transfer|withdraw|send.?money|book|reserve)"
  why: Business-critical endpoints — check for abuse protection

pattern: "(?i)(comment|review|post|register|signup|invite)"
  why: User-generated content endpoints — check for spam protection

pattern: "(?i)(captcha|recaptcha|hcaptcha|turnstile)"
  why: Bot protection — verify it exists on sensitive flows
```

### Semantic Queries

- "Are business-critical operations (purchase, transfer, booking) protected against automated abuse?"
- "Is there rate limiting, CAPTCHA, or fraud detection on high-value workflows?"
- "Can bots automate account creation, content posting, or transactions?"

### CWE IDs

CWE-799, CWE-837

### Remediation

Add CAPTCHA or proof-of-work to public-facing critical flows. Implement velocity checks (e.g., max purchases per hour). Use device fingerprinting or behavioral analysis for fraud detection. Rate limit by IP and by user account.

---

## API7: Server-Side Request Forgery (SSRF)

API accepts URLs from users and fetches them server-side without validation.

### Grep Patterns

```
pattern: "(?i)(fetch|axios|got|request|http\.get|urllib)\s*\(.*req\."
  why: Server-side HTTP request using user-supplied URL

pattern: "(?i)(webhook|callback|redirect|proxy|imageUrl|avatarUrl|url)\s*[:=].*req\."
  why: URL parameter that may trigger server-side fetch

pattern: "(?i)new\s+URL\s*\(.*req\."
  why: URL construction from user input
```

### Semantic Queries

- "Where does the API fetch remote URLs provided by users (webhooks, image proxies, redirects)?"
- "Are user-supplied URLs validated against an allowlist before the server fetches them?"

### CWE IDs

CWE-918

### Remediation

Validate URLs against an allowlist of permitted domains. Block private/internal IP ranges. Disable HTTP redirects in server-side clients. Use a dedicated egress proxy for outbound requests. Validate URL scheme (only allow `https`).

---

## API8: Security Misconfiguration

API-specific misconfigurations: permissive CORS, missing headers, verbose errors, exposed debug endpoints.

### Grep Patterns

```
pattern: "(?i)Access-Control-Allow-Origin.*\*"
  why: Wildcard CORS — API accepts requests from any origin

pattern: "(?i)Access-Control-Allow-Credentials.*true"
  why: Combined with permissive origin, this is critical

pattern: "(?i)(swagger|openapi|api-docs|graphql.?playground|graphiql)"
  why: API documentation/playground exposed — should be disabled in production

pattern: "(?i)error\.(stack|message).*res\.(json|send)"
  why: Internal error details leaked in API responses

pattern: "(?i)X-Powered-By|Server:"
  why: Information disclosure headers
```

### Semantic Queries

- "Is the CORS policy restrictive with a specific origin allowlist?"
- "Are API docs, Swagger UI, or GraphQL playgrounds disabled in production?"
- "Do API error responses leak stack traces or internal details?"

### CWE IDs

CWE-16, CWE-388, CWE-942

### Remediation

Set CORS to specific allowed origins, never wildcard with credentials. Disable API documentation endpoints in production. Return generic error messages — log detailed errors server-side only. Remove `X-Powered-By` and `Server` headers.

---

## API9: Improper Inventory Management

Exposed old API versions, undocumented endpoints, or shadow APIs.

### Grep Patterns

```
pattern: "(?i)/v1/|/api/v1|/legacy/|/old/|/deprecated/"
  why: Old API versions that may lack security controls

pattern: "(?i)/test/|/debug/|/dev/|/staging/"
  why: Non-production endpoints that may be deployed

pattern: "(?i)@deprecated|DEPRECATED|TODO.*remove"
  why: Deprecated code that should be removed
```

### Semantic Queries

- "Are there old API versions (v1, legacy) still accessible alongside current versions?"
- "Are there test, debug, or staging endpoints deployed in production?"
- "Are all API endpoints documented and intentionally exposed?"

### CWE IDs

CWE-1059

### Remediation

Decommission old API versions when new ones are stable. Remove test/debug/staging endpoints from production builds. Maintain an API inventory and audit for undocumented endpoints. Use environment-based feature flags to ensure dev-only routes are not deployed.

---

## API10: Unsafe Consumption of APIs

Application trusts third-party API responses without validation.

### Grep Patterns

```
pattern: "(?i)(\.json\(\)|\.data)\s*\.\s*\w+"
  why: Accessing third-party response fields without schema validation

pattern: "(?i)(fetch|axios|got)\s*\(.*https?://"
  why: External API calls — check if responses are validated

pattern: "(?i)\.pipe\s*\(|stream.*response"
  why: Streaming external content to client without inspection
```

### Semantic Queries

- "Where does the application consume third-party APIs, and are the responses validated against a schema?"
- "Are third-party API responses used directly in database queries or HTML rendering without sanitization?"
- "Is TLS certificate validation enforced on outbound API calls?"

### CWE IDs

CWE-20, CWE-295, CWE-346

### Remediation

Validate all third-party API responses against a schema (e.g., Zod, Joi, JSON Schema). Never trust external data — sanitize before use in queries or rendering. Enforce TLS verification on all outbound calls. Set timeouts and circuit breakers for external dependencies.
