# Frontend Audit Checklist

Load during Phase 3 for any client-side target: web SPAs, server-rendered frontends, mobile webviews, browser extensions. Work every category; mark reviewed-clean explicitly.

## 1. XSS and DOM safety
- Audit every sink: `innerHTML`, `outerHTML`, `dangerouslySetInnerHTML`, `v-html`, `document.write`, `insertAdjacentHTML`, `eval`/`new Function`, `setTimeout(string)`, attribute injection (`href`, `src`, `srcdoc`, event handlers).
- Any user- or API-derived data reaching a sink must be sanitized with a maintained library (DOMPurify-class) or rendered through the framework's escaping — verify the escaping isn't bypassed.
- URL handling: `javascript:` and `data:` schemes rejected on user-supplied links; `target="_blank"` paired with `rel="noopener noreferrer"`.
- Framework escape hatches enumerated and each one justified.

## 2. Content Security Policy and headers
- CSP present and meaningful: no `unsafe-inline`/`unsafe-eval` without documented necessity; nonces or hashes for legit inline needs.
- `frame-ancestors` (clickjacking), `X-Content-Type-Options: nosniff`, `Referrer-Policy` set; HSTS on the serving layer.
- Subresource Integrity on third-party scripts loaded from CDNs.

## 3. Authentication artifacts in the browser
- Where tokens live: prefer HttpOnly, Secure, SameSite cookies for session credentials; if localStorage/sessionStorage is used for tokens, treat as a finding and assess the XSS blast radius explicitly.
- Tokens never in URLs (history, referer leakage, logs).
- Logout clears all client-side auth state; expired-token handling doesn't leave the UI in an authenticated-looking state.
- Sensitive data not cached: `Cache-Control` on authenticated API responses, no secrets in service-worker caches.

## 4. CSRF and request forgery
- State-changing requests protected: SameSite cookies plus token or custom-header verification; verify the backend actually enforces it, not just that the frontend sends it.
- WebSocket/SSE connections authenticate and validate origin.

## 5. Client-side trust boundaries
- No security decision made client-side only: hidden UI is not authorization; every "disabled button" has a server check behind it — cross-reference with the backend audit.
- Prices, quantities, roles, feature flags: anything the client sends that the server should compute or verify.
- Secrets in the bundle: API keys, endpoints, feature flags — grep the built artifacts, not just the source. Anything shipped to the browser is public.
- Source maps: not exposing server-side code or comments in production, or deliberately accepted.

## 6. Third-party and supply chain
- Inventory of third-party scripts, SDKs, analytics, tag managers; each has DOM/data access — justify or remove.
- npm audit posture; lockfile committed; no install-script-heavy or typosquat-suspicious packages.
- postMessage: origin checked on every listener; no wildcard target origins with sensitive payloads.
- iframes sandboxed; embedded content permissions minimal.

## 7. State management coherence (pre-mortem territory)
- Race conditions in async state: two in-flight requests resolving out of order overwriting each other; stale closure over state; optimistic updates without rollback on failure.
- Cache invalidation: mutations invalidating the queries they affect; stale reads after write.
- Derived state duplicated instead of computed — the copies will diverge.
- Error states representable and rendered: every loading state has a failure counterpart; the UI cannot silently show stale data as fresh.
- Form state: double-submit prevented, navigation-away with unsaved changes handled, server validation errors mapped back to fields.

## 8. Data exposure in the UI
- Personal or sensitive data not rendered to users who shouldn't see it even if the API over-returns (defense in depth, but the API over-return is the primary finding).
- No sensitive data in analytics events, error reporters (Sentry-class breadcrumbs), or console logs left in production.
- Clipboard, autofill, and browser-extension exposure considered for high-sensitivity fields.

## 9. Accessibility and correctness as robustness
- Keyboard-only and screen-reader paths exist for critical flows — inaccessible flows hide untested code paths.
- i18n/timezone handling: dates rendered from a single source of truth; no client-locale-dependent business logic.
- Numeric handling: floating point on money, precision loss on large IDs (JS number vs 64-bit int from API) — classic incoherence generator.

## 10. Build and delivery
- Production build actually minifies/strips dev tooling (React devtools hooks, debug flags, verbose logging).
- Environment separation: no staging endpoints or credentials reachable from production bundles.
- Dependency versions of the runtime (browserslist/polyfills) coherent with the user base actually supported.
