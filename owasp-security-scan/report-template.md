# Security Audit Report Template

Use this template to produce the final summary report after all OWASP categories have been scanned. Replace all bracketed placeholders with actual values.

---

```markdown
# Security Audit Report

**Project**: [project name]
**Date**: [scan date]
**Scanned by**: OWASP Security Expert (Cursor Agent)
**Standards**: OWASP Top 10 (2021), OWASP API Security Top 10 (2023)

---

## Executive Summary

| Severity        | Count |
|-----------------|-------|
| Critical        | [n]   |
| High            | [n]   |
| Medium          | [n]   |
| Low             | [n]   |
| Informational   | [n]   |
| **Total**       | [n]   |

[1-2 sentence summary of the overall security posture and most urgent issues.]

---

## Scan Scope

**Tech Stack**: [e.g., Next.js 14, React 18, TypeScript 5, Supabase, Tailwind CSS]

**Files Scanned**: [total count or description of scope]

**Directories Included**:
- [list directories scanned]

**Directories Excluded**:
- node_modules/
- .next/
- [any others]

**Categories Scanned**:
- [x] A01: Broken Access Control
- [x] A02: Cryptographic Failures
- [x] A03: Injection
- [x] A04: Insecure Design
- [x] A05: Security Misconfiguration
- [x] A06: Vulnerable and Outdated Components
- [x] A07: Identification and Authentication Failures
- [x] A08: Software and Data Integrity Failures
- [x] A09: Security Logging and Monitoring Failures
- [x] A10: Server-Side Request Forgery
- [x] API1: Broken Object Level Authorization
- [x] API2: Broken Authentication
- [x] API3: Broken Object Property Level Authorization
- [x] API4: Unrestricted Resource Consumption
- [x] API5: Broken Function Level Authorization
- [x] API6: Unrestricted Access to Sensitive Business Flows
- [x] API7: Server-Side Request Forgery
- [x] API8: Security Misconfiguration
- [x] API9: Improper Inventory Management
- [x] API10: Unsafe Consumption of APIs

---

## Findings Summary

| # | Severity | CWE | Category | File | Description |
|---|----------|-----|----------|------|-------------|
| 1 | [Critical/High/Medium/Low/Info] | CWE-[id] | [A01/API3/etc.] | [file:line] | [short description] |
| 2 | ... | ... | ... | ... | ... |

---

## Detailed Findings

### Finding [#]: [Title]

- **Severity**: [Critical/High/Medium/Low/Informational]
- **CWE**: CWE-[id] — [CWE name]
- **Category**: [OWASP category code and name]
- **File**: [file path:line number]

**Description**:
[What the vulnerability is and why it matters.]

**Evidence**:
```[language]
[code snippet showing the vulnerable code]
```

**Remediation**:
[How to fix, with a corrected code example.]

```[language]
[corrected code snippet]
```

---

[Repeat "### Finding [#]" section for each finding]

---

## Recommendations

Prioritized action items based on scan findings:

1. **[Immediate]**: [Action for critical/high findings]
2. **[Short-term]**: [Action for medium findings]
3. **[Ongoing]**: [Action for low/informational findings and process improvements]

---

## Limitations

This scan is a static code analysis performed by an AI agent. It does not replace:
- Dynamic application security testing (DAST)
- Penetration testing
- Dependency vulnerability scanning (run `npm audit` or equivalent)
- Infrastructure security review
- Compliance audits

False negatives are possible. Findings should be verified by a security professional before remediation.
```
