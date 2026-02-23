# Next.js / React / TypeScript Security Guidance

Apply these checks when the project uses Next.js, React, or TypeScript. These supplement the main OWASP categories with framework-specific patterns.

---

## Next.js Security

### Security Headers (next.config.js / next.config.ts / middleware.ts)

```
Glob: next.config.*, middleware.*
```

Check for presence of these headers (absence is a finding):

| Header | Purpose |
|--------|---------|
| `Content-Security-Policy` | Prevents XSS, injection, clickjacking |
| `Strict-Transport-Security` | Forces HTTPS |
| `X-Frame-Options` | Prevents clickjacking (legacy, supplement with CSP frame-ancestors) |
| `X-Content-Type-Options` | Prevents MIME sniffing |
| `Referrer-Policy` | Controls referrer leakage |
| `Permissions-Policy` | Restricts browser features |

```
pattern: "(?i)headers\s*(\(\)|:)\s*"
  why: Verify security headers are set in next.config or middleware

pattern: "(?i)Content-Security-Policy"
  why: CSP should be configured — check for unsafe-inline, unsafe-eval

pattern: "(?i)unsafe-inline|unsafe-eval"
  why: Weakens CSP — avoid unless absolutely necessary
```

### Server Actions (`"use server"`)

```
pattern: "\"use server\"|'use server'"
  why: Server Action files — every exported function is a public HTTP endpoint

pattern: "(?i)formData\.get\(|formData\.getAll\("
  why: User input from forms — must be validated before use
```

**Key checks:**
- Every Server Action export is a publicly callable endpoint. Verify authorization within each function.
- All `formData.get()` values must be validated (type, length, format) before use.
- Server Actions should not expose internal data in error messages.

### Route Handlers (app/api/**/route.ts)

```
Glob: **/app/**/route.ts, **/app/**/route.js
pattern: "export\s+(async\s+)?function\s+(GET|POST|PUT|DELETE|PATCH)"
  why: API route handlers — check for auth, input validation, error handling
```

**Key checks:**
- Each handler should verify authentication/authorization.
- Request body should be parsed and validated with a schema (Zod, etc.).
- Error responses should not leak internal details.

### Middleware (middleware.ts)

```
Glob: middleware.ts, middleware.js, src/middleware.ts, src/middleware.js
pattern: "(?i)NextResponse\.(next|redirect|rewrite)"
  why: Middleware decisions — verify auth checks before NextResponse.next()

pattern: "(?i)matcher.*config"
  why: Middleware matcher config — check it covers protected routes
```

**Key checks:**
- Middleware should enforce authentication on protected routes.
- `matcher` config should not accidentally exclude routes needing protection.
- Redirect/rewrite targets should not be derived from unsanitized user input.

### Environment Variable Exposure

```
pattern: "NEXT_PUBLIC_"
  why: NEXT_PUBLIC_ variables are exposed to the browser — no secrets here

pattern: "(?i)process\.env\.(DATABASE|DB_|SUPABASE_SERVICE|SECRET|PRIVATE|API_KEY|AUTH)"
  why: Sensitive env vars — verify they are NOT prefixed with NEXT_PUBLIC_
```

**Key checks:**
- `NEXT_PUBLIC_*` variables are bundled into client JavaScript. Never use this prefix for secrets.
- Verify `.env.local` / `.env` files are in `.gitignore`.
- Server-only env vars should only be accessed in server components, Server Actions, or route handlers.

### Data Fetching (Server Components, getServerSideProps, getStaticProps)

```
pattern: "(?i)getServerSideProps|getStaticProps"
  why: Data fetching — ensure sensitive data is not passed as props to client

pattern: "(?i)export\s+default\s+(async\s+)?function.*\{.*fetch\("
  why: Server component data fetching — check for data leakage to client
```

**Key checks:**
- Server-fetched data passed as props to client components is serialized into the HTML. Never include secrets, internal IDs, or excessive data.
- `getStaticProps` output is written to a JSON file — verify it contains only public data.

### Image Optimization

```
pattern: "(?i)remotePatterns|images.*domains"
  why: Next.js image optimization config — overly permissive patterns allow SSRF

pattern: "(?i)remotePatterns.*protocol.*hostname.*\*"
  why: Wildcard in image remote patterns — potential SSRF vector
```

---

## React Security

### XSS Sinks

```
pattern: "dangerouslySetInnerHTML"
  why: Direct HTML injection — input MUST be sanitized with DOMPurify or equivalent

pattern: "(?i)href\s*=\s*\{.*(?:user|input|query|param|data|value)"
  why: Dynamic href — check for javascript: protocol injection

pattern: "(?i)href\s*=.*javascript\s*:"
  why: Direct javascript: protocol in href — XSS

pattern: "(?i)\.innerHTML\s*="
  why: Direct DOM manipulation — XSS if value is user-controlled

pattern: "(?i)createObjectURL|URL\.createObjectURL"
  why: Blob URLs from user input — potential XSS vector

pattern: "(?i)window\.open\s*\(.*(?:user|input|query|param)"
  why: Window.open with user-controlled URL — check for validation
```

### State and Props Leakage

```
pattern: "(?i)console\.(log|debug|info)\s*\(.*(?:password|token|secret|key|auth)"
  why: Sensitive data logged to browser console

pattern: "(?i)JSON\.stringify\s*\(.*(?:user|session|auth|profile)"
  why: Full object serialization — may include sensitive fields
```

### Third-Party Component Risks

```
pattern: "(?i)iframe\s|<iframe"
  why: Embedded iframes — check sandbox attribute and src origin

pattern: "(?i)postMessage\s*\("
  why: Cross-origin messaging — verify origin check on message receipt

pattern: "(?i)addEventListener\s*\(\s*['\"]message['\"]"
  why: Message event listener — must validate event.origin
```

**Key checks for postMessage:**
- `addEventListener("message", ...)` must check `event.origin` against an allowlist.
- `postMessage` should specify a target origin (not `"*"`).

---

## TypeScript Security

### Type Safety on Security-Critical Code

```
pattern: ":\s*any\b"
  why: any type suppresses type checking — critical if on auth, validation, or query code

pattern: "(?i)as\s+any|@ts-ignore|@ts-expect-error"
  why: Type assertion overrides — check if they bypass security-relevant type checks

pattern: "(?i)!\s*\."
  why: Non-null assertion — may hide null/undefined that should be validated
```

### Input Validation Types

```
pattern: "(?i)(zod|yup|joi|superstruct|valibot|ajv|typebox)"
  why: Schema validation library — good (verify it's used on all inputs)

pattern: "(?i)z\.(object|string|number|array)\s*\("
  why: Zod schema definition — verify all API/form inputs are validated
```

**Key checks:**
- All API route handlers and Server Actions should validate input using a runtime schema validator (Zod, Joi, etc.). TypeScript types alone do not provide runtime validation.
- `as any` or `@ts-ignore` on security-critical paths (auth, authorization, data access) should be flagged.

### Sensitive Type Definitions

```
pattern: "(?i)(interface|type)\s+.*(User|Account|Session|Auth|Token)"
  why: Security-relevant type definitions — check for password/secret fields and their handling
```

**Key checks:**
- User/Account types that include `password` or `passwordHash` fields — verify these are excluded from API responses and client-side code.
- Session/Token types — verify they include expiration fields.
