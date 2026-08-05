# Backend Audit Checklist

Load during Phase 3 for any server-side target: APIs, services, workers, CLIs, libraries, infrastructure code. Work every category; mark reviewed-clean explicitly.

## 1. Authentication
- Password storage: modern KDF (argon2id, scrypt, bcrypt) with sane parameters; no MD5/SHA-family, no home-grown schemes.
- Credential comparison in constant time; no user enumeration via error messages or timing.
- Session/token lifecycle: expiry enforced server-side, revocation actually checked, refresh rotation, logout invalidates.
- JWT: algorithm pinned (no `alg: none`, no RS/HS confusion), signature verified before any claim is read, `aud`/`iss`/`exp` validated, keys rotated.
- MFA/step-up paths cannot be skipped by hitting the post-MFA endpoint directly.

## 2. Authorization
- Every endpoint/handler checks authorization — enumerate them and verify; the missing check is the classic finding.
- Object-level checks (IDOR/BOLA): resource ownership verified on every read AND write, not just on list.
- Privilege boundaries: role checks server-side only; no trust in client-supplied roles, scopes, or IDs.
- Multi-tenancy: tenant ID derived from the authenticated context, never from request parameters; every query filtered by tenant.
- Indirect paths: admin actions reachable via batch endpoints, GraphQL resolvers, or background jobs that skip the middleware.

## 3. Input handling and injection
- SQL/NoSQL: parameterized everywhere; audit every string-built query, ORM `raw` escape hatches included.
- Command execution: no shell interpolation of untrusted data; argument arrays over shell strings.
- Path traversal: user input never concatenated into filesystem paths without canonicalization + allowlist.
- Deserialization: no unsafe deserialization of untrusted data (pickle, Java native, YAML load-unsafe).
- SSRF: user-supplied URLs validated against allowlists; internal metadata endpoints (169.254.169.254, etc.) unreachable.
- Template injection, header injection (CRLF), XML (XXE disabled), regex (catastrophic backtracking on untrusted input).
- File uploads: type validated by content not extension, size-bounded, stored outside the web root, never executed.

## 4. Secrets and cryptography
- No secrets in code, git history, logs, error messages, or client-delivered artifacts. Grep for key/token/password patterns and entropy.
- Secrets loaded from a manager or env at runtime; rotation possible without redeploy.
- Crypto: vetted libraries only; authenticated encryption (AEAD); no ECB; IVs/nonces unique; randomness from CSPRNG only.
- TLS enforced for every external and internal hop where the threat model requires it; certificate validation never disabled.
- Sensitive material zeroized or scoped tightly in memory where the language allows; never in Debug output.

## 5. Data exposure and privacy
- Error responses: no stack traces, SQL fragments, or internal paths to clients; detailed logs server-side only.
- Logging: no credentials, tokens, or personal data in logs; log injection (newline smuggling) prevented.
- API responses: no over-fetching (returning whole entities where a projection is needed); soft-deleted or unauthorized fields not serialized by accident.
- Personal data: retention and erasure paths exist and actually delete; backups considered.

## 6. Error handling and resilience
- Every recoverable error handled or deliberately propagated; empty catch blocks and ignored results are findings.
- Failure of external calls: timeouts set on every network call, retries bounded with backoff and idempotency, circuit-breaking where cascading failure is possible.
- Resource cleanup on error paths: connections, file handles, locks released (audit early returns and panics/exceptions).
- Graceful degradation defined: what happens when the cache, queue, or a dependency is down.

## 7. Concurrency and state
- Shared mutable state identified; every access synchronized or the type system proves exclusivity.
- Check-then-act races (exists-then-create, read-then-update); TOCTOU on filesystem operations.
- Database: transaction boundaries match invariants; isolation level sufficient; optimistic locking or row locks where lost updates matter; unique constraints backing application-level uniqueness checks.
- Idempotency keys on externally-triggered mutations (webhooks, payment callbacks, retried jobs).

## 8. API and protocol hygiene
- Rate limiting on authentication, expensive, and enumeration-prone endpoints.
- Pagination enforced; no unbounded list endpoints.
- HTTP: correct methods and status codes; no state change on GET; mass-assignment prevented (explicit field allowlists on binding).
- CORS: no wildcard-with-credentials; origin allowlists exact-match.
- Versioning/compat: breaking changes detectable; unknown fields handled deliberately.

## 9. Infrastructure and configuration
- Containers: non-root user, minimal base image, no secrets in layers or build args, health checks defined.
- IaC: security groups least-privilege, storage buckets not public unless intended, encryption at rest enabled.
- Debug/dev modes, default credentials, and sample endpoints absent from production configuration.
- Migrations reversible; destructive migrations gated.

## 10. Observability as a security control
- Auth failures, privilege changes, and sensitive operations produce audit events.
- Alerting exists for anomalous patterns (brute force, mass export).
- Trace/correlation IDs propagate without leaking sensitive context.
