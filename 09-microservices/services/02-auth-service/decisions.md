# Technical Decisions — Auth Service

---

## Decision: JWT signing algorithm — RS256 vs HS256

**Decision:** HMAC with a shared secret (`JWT_SECRET`).

**Why:** the three services validate the token by themselves without a key distribution endpoint;
with only three services under the same team, one secret stored as an environment variable is simpler
than a key pair and a JWKS endpoint.

**Safeguards:** `JwtKeyProvider` rejects an empty secret and the value that was once published in the
repository, so the service does not start with them. The secret was rotated on 28/10/2026.

---

## Decision: Refresh tokens in the database vs. stateless

**Decision:** the refresh token is a JWT valid 15 days, and its hash is stored per device in
`identity.device_session` with status `active`, `expired` or `revoked`.

**Why:** a session can be revoked per phone, and the app can tell the user when her account signs in
from a new phone. The mobile app refreshes the access token automatically when it receives a 401.

---

## Decision: Where per-resource permission checks live

**Decision:** in each service's use cases. The owner of alerts, contacts, locations, routes and zone
reports is always taken from the JWT, never from the request body. Admin endpoints use
`@PreAuthorize("hasRole('ADMIN')")`.

**Why:** the web guards only guide the navigation; the access decision must be made on the server.

---

## Decision: Account lockout policy

**Decision:** there is no automatic lockout after failed attempts. Brute force is limited with
`RateLimitFilter` (5 attempts per minute per IP, then 429). Accounts are blocked manually by an
administrator, with a reason and a capture, and login with a blocked account answers 401 "Account
blocked".

**Why:** an automatic lockout could be used to block a victim's account on purpose and stop her from
activating alerts.

---

## Correlations

- Cross-cutting authentication → `05-architecture/cross-cutting.md`
- Security threat model → `05-architecture/security-threat-model.md`
