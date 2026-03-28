---
name: security-scan
description: "Security vulnerability scan using the Security Vulnerability Scanner agent workflow. Use when the user says security scan, check for vulnerabilities, security audit, is this secure, OWASP check, or before deploying to production."
---

## Context

- Current branch: !`git branch --show-current`
- Recently modified files: !`git diff --name-only HEAD~5`

## Your Task

Run a security vulnerability scan on the following:

**Target:** $ARGUMENTS

If no target is specified, scan recently modified files and critical paths (auth, API endpoints, data handling).

### Scan Categories

Systematically analyze for:

1. **Injection** — SQL, NoSQL, command, LDAP, XPath
2. **Authentication & Session** — Weak auth, session fixation, token handling
3. **XSS** — Reflected, stored, DOM-based
4. **IDOR** — Insecure direct object references
5. **Security Misconfiguration** — Default configs, unnecessary features, verbose errors
6. **Sensitive Data Exposure** — Unencrypted data, leaked secrets, excessive logging
7. **Access Control** — Missing authorization, privilege escalation
8. **CSRF** — Cross-site request forgery
9. **Known Vulnerabilities** — Dependencies with CVEs
10. **Logging & Monitoring** — Insufficient audit trails
11. **Race Conditions** — TOCTOU, concurrent access issues
12. **Cryptographic Weaknesses** — Weak algorithms, improper key management
13. **Path Traversal** — File system access control
14. **Deserialization** — Unsafe object deserialization
15. **SSRF** — Server-side request forgery

### Output Format

```
## Security Scan Results

**Scan scope:** [what was scanned]
**Date:** [current date]

### Critical
- [Vulnerability] — `file:line` — [brief description]

### High
- [Vulnerability] — `file:line` — [brief description]

### Medium
- [Vulnerability] — `file:line` — [brief description]

### Low
- [Vulnerability] — `file:line` — [brief description]

---

**Summary:** X critical, X high, X medium, X low issues found.
**Recommendation:** [BLOCK DEPLOY | FIX BEFORE DEPLOY | ACCEPTABLE RISK | CLEAN]
```

Only report actual vulnerabilities, not theoretical concerns. Be specific about location and exploitability. Omit empty severity categories.
