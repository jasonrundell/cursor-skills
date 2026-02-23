# OWASP Top 10 (2021) — Scan Reference

For each category: search every listed pattern, then run the semantic queries.

---

## A01: Broken Access Control

Improper enforcement of access restrictions allowing users to act outside their intended permissions.

### Grep Patterns

```
pattern: "(\.role|\.isAdmin|\.permissions)\s*===?\s*['\"]"
  why: Hardcoded role checks that may miss edge cases

pattern: "(?i)(admin|superuser|root).*password"
  why: Hardcoded admin credentials

pattern: "req\.(params|query)\.(id|userId|user_id|accountId)"
  why: Direct object reference from user input — check for authorization

pattern: "\.findById\(req\."
  why: Database lookup using unsanitized user-supplied ID

pattern: "(?i)#.*TODO.*(auth|permission|access)"
  why: Unfinished authorization logic
```

### Semantic Queries

- "Where are database records fetched using IDs from request parameters without checking ownership?"
- "Are there API endpoints missing authentication or authorization middleware?"
- "Where can users access resources belonging to other users?"

### CWE IDs

CWE-284, CWE-285, CWE-639, CWE-862, CWE-863

### Remediation

Enforce server-side access control on every request. Use middleware to verify authorization before processing. Never rely on client-side checks alone. Deny by default.

---

## A02: Cryptographic Failures

Sensitive data exposure due to weak or missing cryptography.

### Grep Patterns

```
pattern: "(?i)(md5|sha1|des|rc4|rot13)\s*\("
  why: Weak/deprecated cryptographic algorithms

pattern: "(?i)(password|secret|token|key|apikey|api_key)\s*[:=]\s*['\"][^'\"]{3,}['\"]"
  why: Hardcoded secrets in source code

pattern: "http://"
  why: Unencrypted HTTP connections (check if connecting to external services)

pattern: "(?i)createCipher\(|createDecipher\("
  why: Deprecated Node.js crypto methods (use createCipheriv)

pattern: "(?i)Math\.random\(\)"
  why: Non-cryptographic PRNG used where crypto randomness may be needed

pattern: "(?i)localStorage\.(set|get)Item\(.*(token|password|secret|key)"
  why: Secrets stored in localStorage (accessible to XSS)

pattern: "(?i)setCookie\(.*secure\s*:\s*false"
  why: Cookie without Secure flag
```

### Semantic Queries

- "Where are passwords stored or compared, and is bcrypt/argon2/scrypt used?"
- "Are there API keys, tokens, or credentials committed in the source code?"
- "Where is sensitive data transmitted and is TLS enforced?"

### CWE IDs

CWE-261, CWE-296, CWE-310, CWE-319, CWE-321, CWE-327, CWE-328, CWE-330, CWE-522

### Remediation

Use strong algorithms (AES-256-GCM, bcrypt/argon2 for passwords, SHA-256+ for hashing). Never hardcode secrets — use environment variables or a secrets manager. Enforce HTTPS everywhere. Use `crypto.randomBytes()` or `crypto.getRandomValues()` for security-sensitive randomness.

---

## A03: Injection

Untrusted data sent to an interpreter as part of a command or query.

### Grep Patterns

```
pattern: "(?i)(query|execute|exec)\s*\(\s*[`'\"].*\$\{|.*\+\s*req\."
  why: SQL query with string interpolation/concatenation from user input

pattern: "(?i)eval\s*\("
  why: Dynamic code execution

pattern: "(?i)new\s+Function\s*\("
  why: Dynamic function construction

pattern: "(?i)child_process\.(exec|execSync|spawn)\s*\(.*req\."
  why: OS command injection via user input

pattern: "(?i)\.innerHTML\s*=|\.outerHTML\s*="
  why: DOM-based XSS sink

pattern: "dangerouslySetInnerHTML"
  why: React XSS sink — verify input is sanitized

pattern: "(?i)\$where|\$regex.*req\."
  why: MongoDB injection via query operators

pattern: "(?i)serialize|unserialize|pickle\.loads|yaml\.load\("
  why: Deserialization of untrusted data

pattern: "(?i)document\.(write|writeln)\s*\("
  why: DOM XSS sink

pattern: "(?i)\.raw\s*\(|\.rawQuery\s*\("
  why: Raw SQL query — verify parameterization
```

### Semantic Queries

- "Where is user input concatenated into SQL, NoSQL, or OS commands?"
- "Where is user-supplied HTML rendered without sanitization?"
- "Are there template injection points where user input reaches template engines?"

### CWE IDs

CWE-20, CWE-74, CWE-77, CWE-78, CWE-79, CWE-89, CWE-94, CWE-116, CWE-943

### Remediation

Use parameterized queries/prepared statements for all database access. Use `child_process.execFile` with argument arrays instead of `exec` with string interpolation. Sanitize HTML with DOMPurify or equivalent before rendering. Avoid `eval()`, `new Function()`, and dynamic code execution entirely.

---

## A04: Insecure Design

Flaws in the application's architecture or design that cannot be fixed by implementation alone.

### Grep Patterns

```
pattern: "(?i)(rate.?limit|throttle|brute.?force)"
  why: Check if rate limiting is implemented (absence is the vulnerability)

pattern: "(?i)captcha|recaptcha"
  why: Check if bot protection exists on sensitive forms

pattern: "(?i)password.*length.*(4|5|6)[^0-9]"
  why: Weak password policy (minimum length too low)
```

### Semantic Queries

- "Is rate limiting implemented on authentication and sensitive endpoints?"
- "Are there business logic workflows that can be abused (e.g., coupon reuse, unlimited retries)?"
- "Is there a password policy enforcing minimum strength?"
- "Are there anti-automation controls on public-facing forms?"

### CWE IDs

CWE-73, CWE-183, CWE-209, CWE-256, CWE-501, CWE-522

### Remediation

Implement rate limiting on authentication and sensitive operations. Enforce strong password policies. Add CAPTCHA to public forms. Design with threat modeling — consider abuse cases alongside use cases.

---

## A05: Security Misconfiguration

Missing or incorrect security settings across the application stack.

### Grep Patterns

```
pattern: "(?i)(debug|DEBUG)\s*[:=]\s*(true|1|['\"]true['\"])"
  why: Debug mode enabled (may leak stack traces, config)

pattern: "(?i)Access-Control-Allow-Origin.*\*"
  why: Wildcard CORS policy

pattern: "(?i)(helmet|security.?headers|X-Frame-Options|Content-Security-Policy)"
  why: Check if security headers are configured (absence is a finding)

pattern: "(?i)directory.?listing|autoindex\s+on"
  why: Directory listing enabled

pattern: "(?i)stack.?trace|stackTrace|verbose.?error"
  why: Verbose error messages exposed to users

pattern: "(?i)\"(X-Powered-By|Server)\":\s*"
  why: Information disclosure via response headers
```

### Semantic Queries

- "Where are HTTP security headers configured (CSP, HSTS, X-Frame-Options, X-Content-Type-Options)?"
- "Are error messages exposing stack traces or internal details to users?"
- "Is CORS configured restrictively or does it allow all origins?"
- "Are default credentials or configurations left unchanged?"

### CWE IDs

CWE-2, CWE-11, CWE-13, CWE-15, CWE-16, CWE-388, CWE-942

### Remediation

Disable debug mode in production. Set restrictive CORS policies. Add security headers (CSP, HSTS, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy). Remove `X-Powered-By` header. Return generic error messages to users.

---

## A06: Vulnerable and Outdated Components

Using libraries/frameworks with known vulnerabilities.

### Grep Patterns

```
Glob: package.json, package-lock.json, yarn.lock, pnpm-lock.yaml
  why: Check dependency versions against known vulnerabilities

pattern: "(?i)(lodash|moment|jquery|express|axios).*['\"][\^~]?\d+\.\d+\.\d+['\"]"
  why: Common packages — check if versions have known CVEs
```

### Semantic Queries

- "Are there dependencies pinned to versions with known security vulnerabilities?"
- "Is there a dependency update or audit process configured?"

### CWE IDs

CWE-1035, CWE-1104

### Remediation

Run `npm audit` or equivalent regularly. Keep dependencies updated. Use automated tools (Dependabot, Renovate, Snyk) for dependency monitoring. Remove unused dependencies. Pin exact versions in production.

---

## A07: Identification and Authentication Failures

Weaknesses in authentication mechanisms.

### Grep Patterns

```
pattern: "(?i)jwt\.(sign|verify)\s*\("
  why: Check JWT implementation — verify algorithm, expiration, secret strength

pattern: "(?i)algorithm.*['\"]none['\"]"
  why: JWT "none" algorithm vulnerability

pattern: "(?i)(session|cookie).*(httpOnly|HttpOnly)\s*:\s*false"
  why: Session cookie without HttpOnly flag

pattern: "(?i)expiresIn.*['\"](\d{1,2}d|[3-9]\d{2,}h|never)['\"]"
  why: Excessively long token/session lifetime

pattern: "(?i)bcrypt\.compare|argon2|scrypt|pbkdf2"
  why: Verify password hashing is used (absence is a finding)

pattern: "(?i)password\s*===?\s*req\."
  why: Plain-text password comparison
```

### Semantic Queries

- "How are user sessions managed and are they invalidated on logout?"
- "Is multi-factor authentication supported for sensitive operations?"
- "How are password reset flows implemented — are they vulnerable to account takeover?"
- "Are failed login attempts tracked and rate-limited?"

### CWE IDs

CWE-255, CWE-259, CWE-287, CWE-288, CWE-306, CWE-307, CWE-384, CWE-613, CWE-620, CWE-640

### Remediation

Use strong password hashing (bcrypt with cost >= 10, argon2id). Set `httpOnly`, `secure`, and `sameSite` flags on session cookies. Implement account lockout or progressive delays after failed attempts. Enforce short token lifetimes with refresh rotation. Never use JWT algorithm "none".

---

## A08: Software and Data Integrity Failures

Code or infrastructure that does not verify integrity of updates, data, or CI/CD pipelines.

### Grep Patterns

```
pattern: "(?i)integrity\s*=|crossorigin"
  why: Check if SRI (Subresource Integrity) is used for CDN scripts

pattern: "(?i)(cdn|unpkg|jsdelivr|cloudflare)\.(com|net)"
  why: External CDN resources — check for SRI hashes

pattern: "(?i)JSON\.parse\s*\(.*req\."
  why: Deserialization of user-supplied JSON without validation

pattern: "(?i)\|\s*safe\b|\{\{.*\|.*raw"
  why: Template engine marking user content as safe/raw
```

### Semantic Queries

- "Are external scripts loaded with Subresource Integrity (SRI) hashes?"
- "Is the CI/CD pipeline secured against code injection?"
- "Are serialized objects validated before deserialization?"

### CWE IDs

CWE-345, CWE-353, CWE-426, CWE-494, CWE-502, CWE-565, CWE-784, CWE-829, CWE-830

### Remediation

Add SRI hashes to all externally loaded scripts and stylesheets. Validate and schema-check all deserialized data. Secure CI/CD pipelines with signed commits and restricted permissions. Use `JSON.parse` only on validated input.

---

## A09: Security Logging and Monitoring Failures

Insufficient logging of security events making breach detection difficult.

### Grep Patterns

```
pattern: "(?i)(console\.(log|warn|error)|logger\.(info|warn|error|debug))"
  why: Check what is being logged — sensitive data should not appear in logs

pattern: "(?i)(password|secret|token|credit.?card|ssn).*log"
  why: Sensitive data in log statements

pattern: "(?i)catch\s*\([^)]*\)\s*\{\s*\}"
  why: Empty catch blocks silently swallowing errors
```

### Semantic Queries

- "Are authentication events (login, logout, failed attempts) logged?"
- "Are access control failures logged and monitored?"
- "Is sensitive data (passwords, tokens, PII) accidentally included in log output?"
- "Are logs protected against injection (log forging)?"

### CWE IDs

CWE-117, CWE-223, CWE-532, CWE-778

### Remediation

Log all authentication events, access control failures, and input validation failures. Never log passwords, tokens, or PII. Implement centralized log management. Set up alerts for suspicious patterns (brute force, privilege escalation attempts). Ensure catch blocks at minimum log the error.

---

## A10: Server-Side Request Forgery (SSRF)

Application fetches remote resources using user-supplied URLs without validation.

### Grep Patterns

```
pattern: "(?i)(fetch|axios|got|request|http\.get|https\.get|urllib)\s*\(.*req\."
  why: HTTP request using user-supplied URL

pattern: "(?i)new\s+URL\s*\(.*req\."
  why: URL construction from user input

pattern: "(?i)(redirect|location)\s*[:=].*req\."
  why: Open redirect from user input

pattern: "(?i)image.?(url|src|proxy)|proxy.?url|fetch.?url"
  why: URL parameters that may fetch remote resources
```

### Semantic Queries

- "Where does the application make HTTP requests using user-supplied URLs?"
- "Are there URL validation or allowlist checks before server-side fetches?"
- "Can users trigger the server to connect to internal network addresses?"

### CWE IDs

CWE-918

### Remediation

Validate and allowlist destination URLs before making server-side requests. Block requests to private IP ranges (10.x, 172.16-31.x, 192.168.x, 127.x, 169.254.x, ::1). Disable HTTP redirects in server-side HTTP clients. Use a URL parser to verify scheme and host before fetching.
