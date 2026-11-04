# Security Policy

> Security is not a feature — it is a system property built from day one. This document
> defines the mandatory practices.
> Any deviation must be explicitly approved by the Tech Lead.

---

## Security principles

1. **Defense in Depth:** Multiple security layers. If one fails, the others contain the damage.
2. **Least Privilege:** Each component has only the minimum necessary permissions.
3. **Fail Secure:** In case of error, the system denies access, does not allow it.
4. **Security by Design:** Security controls are designed from the start, not added at the end.
5. **Zero Trust:** Always verify, never implicitly trust, even within the internal network.

---

## Authentication

### JWT (JSON Web Tokens)

| Property | Woman Alert (current) | Note |
|----------|-----------------------|------|
| Signing algorithm | HMAC with `JWT_SECRET` (256+ bit), shared by the three services | The services do not start with an empty secret or with the value that was once published; rotated on 28/10/2026 |
| Access token expiration | 24 hours (`exp`) | — |
| Refresh token expiration | 15 days (hash stored per device in `identity.device_session`) | — |
| Claims | `sub` (users.id), `role`, `iat`, `exp` | No `jti` |
| Client storage | `expo-secure-store` (mobile); `localStorage` (web) | Web storage is an open finding: move it out of `localStorage` |

**Prohibited in the payload:**
- Passwords
- Card data
- Full PII (only the user ID)

### Refresh Token

- Stored in the database (with bcrypt hash)
- Mandatory rotation on each use (one refresh token = one use)
- Invalidated on logout and on password change
- ALL active tokens invalidated if use of a revoked token is detected

---

## Authorization

### RBAC (Role-Based Access Control)

> **Implementation note:** The `identity.role` table stores two values: `user` (id 1) and
> `administrator` (id 2). There are no other roles: moderation is executed by the administrator
> and there is no separate moderator, super admin or viewer role. The JWT carries `role: user` or
> `role: admin`, and the web guards (`authGuard`, `userGuard`, `adminGuard`) use those values.

| Role | Description | Permissions |
|------|-------------|------------|
| `administrator` (`role: admin`) | Administrative panel user | Dashboard, users (list, edit, block with reason), alerts (status, route of active or attended alerts only), zones and help points, reports with PDF/CSV export, evidence review, moderation of user reports, devices, read-only audit log, system configuration, account deletion approved by a second administrator. Never sees the personal data of trusted contacts |
| `user` (`role: user`) | Person who uses the app and the user portal | Own profile, contacts, alerts, locations, routes, trips, automatic protection, evidence (download and keep up to 5) and zone reports. Always limited to her own data |

The first administrator is created by registering normally and changing `role_id` to 2 in the
database; the token carries the new role after logging in again.

**Permission model:**

```
Permission: [resource]:[action]

Woman Alert does not use fine-grained permissions: access is decided by the role
and by the owner of the resource (taken from the JWT). Examples of the rules in code:
  /api/admin/**                 → role admin
  /api/alerts/{id}              → owner of the alert (otherwise 403)
  /api/contacts/{id} PUT/DELETE → owner of the contact
  /api/public/tracking/{code}   → valid tracking code, no session
```

**Validation:**
- There is no API Gateway: each service (Auth, Alert, Evidence) validates the JWT (signature and expiration) with the same key
- Each service checks the role for the specific operation (`@PreAuthorize("hasRole('ADMIN')")` on `/api/admin/**`) and the owner of the resource, taken from the JWT
- The role travels in the JWT as claim `role: user` or `role: admin` (roles `user` and `administrator` in `identity.role`)

---

## Secure communication

### Transmission

- **HTTPS mandatory** in all environments except local
- TLS 1.2 minimum; TLS 1.3 recommended
- Certificates: Let's Encrypt (staging) / Corporate CA (production)
- HSTS enabled in production

### Internal service-to-service communication

- mTLS for service-to-service communication in production (if possible with service mesh)
- Bearer token or internal API key for services that do not support mTLS

---

## Secret management

```
✗ NEVER in source code
✗ NEVER in committed .env
✗ NEVER in logs
✗ NEVER in client error messages
✓ Environment variables (injected by the orchestrator)
✓ Vault (HashiCorp Vault, AWS Secrets Manager, etc.)
✓ Kubernetes Secrets (encrypted with KMS)
```

**Secret rotation:**
- API keys: every 90 days
- TLS certificates: 60 days before expiration
- DB passwords: every 6 months or immediately if compromise is suspected

---

## Input validation and sanitization

### General rules

1. **Never trust user input.** Validate at the edge (controller) before processing.
2. **Whitelist, not blacklist.** Define what is allowed, not only what is prohibited.
3. **Reject early.** If input is invalid, respond 400 and do not process further.

### SQL Injection — Prevention

```typescript
// ✗ VULNERABLE
const result = await db.query(`SELECT * FROM users WHERE email = '${userInput}'`);

// ✓ SAFE — always use prepared parameters
const result = await db.query('SELECT * FROM users WHERE email = $1', [userInput]);
```

### XSS — Prevention

```typescript
// ✗ VULNERABLE — rendering HTML without escaping
element.innerHTML = userProvidedContent;

// ✓ SAFE — use textContent or sanitize
element.textContent = userProvidedContent;
// or with library: DOMPurify.sanitize(userProvidedContent)
```

### Validation with Zod / Joi

```typescript
// Explicit validation schema in the controller
const CreateOrderSchema = z.object({
  clientId: z.string().uuid(),
  items: z.array(z.object({
    productId: z.string().uuid(),
    quantity: z.number().int().positive().max(1000),
    price: z.object({
      amount: z.number().positive(),
      currency: z.enum(['COP', 'USD']),
    }),
  })).min(1).max(50),
});
```

---

## OWASP Top 10 — Review checklist

| Vulnerability | Implemented control |
|---------------|-------------------|
| A01: Broken Access Control | RBAC + permission validation in each service |
| A02: Cryptographic Failures | TLS 1.2+, bcrypt for passwords, secrets in vault |
| A03: Injection | Prepared parameters in SQL, schema validation |
| A04: Insecure Design | Threat modeling in design, Security review |
| A05: Security Misconfiguration | IaC for configuration, review of defaults |
| A06: Vulnerable Components | Dependabot / Snyk for automatic updates |
| A07: Authentication Failures | JWT with rotation, brute-force protection |
| A08: Software Integrity Failures | Verify dependency checksums, SBOM |
| A09: Logging Failures | Logs without PII, centralized, with alerts |
| A10: SSRF | Whitelist of external URLs, do not follow redirects automatically |

---

## Audit and security logs

### Events that are ALWAYS recorded

```typescript
// Security events — store in a separate log, with retention > 1 year
const SECURITY_EVENTS = [
  'auth.login.success',
  'auth.login.failure',
  'auth.login.brute_force_detected',
  'auth.password.changed',
  'auth.token.revoked',
  'auth.unauthorized_access_attempt',
  'data.pii.accessed',
  'admin.role.changed',
  'admin.user.deleted',
];
```

**Required fields in security logs:**
- `accountId` (or `ANONYMOUS` if not authenticated) — maps to `account.id` in the database
- `sourceIp`
- `action`
- `resource`
- `result` (SUCCESS / FAILURE)
- `timestamp`

---

## Vulnerability process

### What to do if you find a vulnerability

1. **Do not commit it to the public repo** or discuss it in open channels
2. Immediately notify the Tech Lead via a private channel
3. Create a private issue or a restricted repository issue
4. Severity is assigned (CVSS score or internal classification)
5. Remediated in the current sprint if critical, in the next sprint if high

### Remediation SLAs

| Severity | Remediation time |
|----------|----------------|
| Critical (CVSS 9-10) | 24 hours |
| High (CVSS 7-8.9) | 1 week |
| Medium (CVSS 4-6.9) | 1 month |
| Low (CVSS < 4) | Next security review |

---

## Correlations

- Security non-functional requirements → `04-requirements/non-functional.md`
- ADR on authentication → `05-architecture/decisions/`
- Security event observability → `13-operations/observability.md`
- RBAC implemented in → `09-microservices/services/02-auth-service/`