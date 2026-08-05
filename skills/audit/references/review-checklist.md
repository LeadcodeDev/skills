# Change-review checklist

What to flag in a diff, staged changes, or a PR. Pair with `code-quality.md`, which carries the structural stance; this file is the sweep.

**Subordinate to the repo**: a flag that conflicts with the target's `CLAUDE.md` / `AGENTS.md` or its established patterns → defer to the repo. Prefer few high-conviction findings over many nits. Don't flag pre-existing debt unless the diff materially worsens it. Skip machine-enforced style.

## Scope & necessity

- Flag anything built beyond what the task needs — speculative feature/param/export/"flexibility".
- Flag reinvented framework/lib/stdlib; a pattern kept by inertia.
- Flag abstraction with 0–1 real callers (wrapper/option = dead weight) → inline.
- Flag DRY-ing before the 3rd occurrence (premature; 2 = coincidence).
- Flag a novel dep/pattern added over a boring/proven one without justification.
- Flag a near-duplicate helper that belongs in an existing module.
- Flag orphans the diff creates (unused import/param/helper). Don't flag pre-existing dead code unless the diff worsens it.
- Flag a compat path with no caller — mode flag / prop / wrapper / route alias / fallback kept "just in case" → delete, not polish (grep callers first).

## Simplicity & structure

- Flag complexity rearranged where it could be deleted.
- Flag ten scattered 5-line helpers where one linear function reads better.
- Flag a shallow module (interface ≈ implementation); a pass-through wrapper; a deletion-test failure (delete it and complexity vanishes rather than scattering); a hard decision exposed as a config knob instead of absorbed.
- Flag internals leaked to callers just for testability.
- Flag a single-call-site function not inlined (unless step-down / own tests / repo expects unit).
- Flag a single-use var that only renames the op (generic name, one adjacent use) → inline; not one that names intent or tames nesting.
- Flag an inline that changes behavior — capture-before-mutation, double/lost eval of a side-effecting call, short-circuit/`await` order, lost type narrowing — these stay even if single-use.
- Flag inherent complexity (DI/routing/parsing) smeared instead of concentrated in a named module.
- Flag business logic duplicated across entry points (http/cli/job/webhook) instead of one operation.
- Flag independent async work serialized where it could run concurrently.
- Flag >~3 indent levels / missing guard clauses; input mutation where new data should be returned.
- Flag clever code: nested ternaries, implicit coercion, multi-op one-liners.
- Flag global/singleton/magic-registry state not traceable from a single root.
- Flag an ad-hoc conditional bolted onto an unrelated flow → own path/helper/typed dispatch.
- Flag feature logic in a shared/general path; policy hardcoded into mechanism.
- Flag a file pushed past the repo norm (~500 LOC / 1k) without strong reason → decompose.

## Module seams & vertical slices

- Flag an organize-by-role split (`services/`/`repositories/`/`handlers/`) where a vertical slice reads better.
- Flag a public surface wider than necessary (Hyrum's Law) — should default private.
- Flag a public seam whose obvious call bypasses required semantics (`setSelection(null)` not clearing; a prop needing a sibling prop) → move the invariant into the seam (safe interface > narrow type > rename/split > helper last).
- Flag a vendor/third-party type in a signature; a dep not wrapped at the seam.
- Flag a module with no nameable absorbed assumption (just a folder).
- Flag domain logic in `shared/`; a helper extracted by anticipation, not real reuse.
- Flag cross-domain reach into internals/DB tables instead of the public interface; N imports where a domain event fits.
- Flag a port/seam with a single real adapter (hypothetical seam — a test fake counts as the 2nd).
- Flag library options left implicit (timeout/creds/redirect) — default-drift risk.
- Flag internals moved without a compat facade where external consumers exist.

## Types & data model

- Flag a representable-but-illegal state — name the union/brand/parser that removes it.
- Flag status strings / parallel booleans instead of one discriminated union; a missing exhaustiveness guard.
- Flag untrusted input consumed without parsing to a typed domain object at the seam; re-validation in loops.
- Flag a handler with no input/output schema; Input not separated from Output (server-set fields accepted).
- Flag a silent fallback masking invalid input (`?? 0`, `|| ''`).
- Flag an escape-hatch type erasing the contract (`any`/`unknown`/untyped cast) with no nearby runtime check.
- Flag a return type heavier than needed (ladder: `void` < `bool` < `T` < `Option<T>` < `Result<T,E>`).
- Flag 4+ positional args / 2+ same-type adjacent (swap risk) → options object.
- Flag a behavior-branching bool/enum param → split into named functions.
- Flag the same 3+ fields traveling together → value object.

## Error handling

- Flag an error missing any of: what op, why, remediation, blast radius; the cause dropped on wrap.
- Flag an assert re-checking already-parsed boundary data; a positive-only invariant (no negative-space check).
- Flag a bare throw / `any` at a lib or interface seam instead of a declared error type.
- Flag an error the caller can't prevent, or one patched after the fact where redefining the contract would design it out of existence.
- Flag an unbounded loop/queue/retry/buffer (no ceiling); I/O with no timeout; env/config not validated at boot.
- Flag an acquired resource (file/conn/lock/listener/subscription) not released on every path including error.
- Flag a swallowed error (a catch that neither handles nor propagates).
- Flag a multi-step mutation with no atomicity/rollback (half-applied state on failure).
- Flag a secret/token/PII in logs, error messages, test fixtures, or commits.
- Flag the process killed from a work unit (`process.exit` outside boot).
- Flag an auth/crypto/PII check skipped inside the trust zone.
- Flag a stack trace or raw provider payload leaked in a seam-crossing error.
- Flag a user-facing message that blames the user or a third party, leaks jargon, or omits what is NOT affected.
- Flag mixed error idioms (Result + exceptions) against the repo's idiom.

## Tests

- Flag untested behavior, prioritizing crossed trust boundaries over pure logic.
- Flag tests asserting implementation rather than behavior (they break on refactor).
- Flag tautological / framework-semantics / passthrough tests; truthiness over specific matchers.
- Flag missing edge coverage (ZOMBIES: zero/one/many/boundary/interface/exception).
- Flag a missing characterization test before a refactor or deletion; a missing contract test at a provider seam.
- Flag mocks of internal collaborators (mock only real boundaries: net/fs/db/time/rand).
- Flag a bug fix with no regression test; a known bug silently skipped instead of expected-fail + issue.
- Flag a flaky test: sleep/wall-clock/network/random, or shared mutable state across tests (order-dependent).
- Flag a snapshot on non-deterministic output, or a broad UI snapshot.

## Naming & hygiene

- Flag a name describing mechanics not intent (`setStatusToClosed`); a filler-only name (process/handle/do/run).
- Flag abbreviations, negative-form booleans (should read is/has/should/can, positive), missing units (`delayMs`/`sizeKb`).
- Flag a verb given a 2nd meaning; overloaded validate/build/resolve; synonym aliases.
- Flag a dishonest escape hatch (no `dangerous_` / `unsafe_` / `experimental_` prefix).
- Flag a comment paraphrasing the next line instead of the why/consequence; a TODO with no action or issue link.
- Flag delivery-relative comments ("this MR/delivery", "follow-up MR", "behavior unchanged", "new in issue #N", "was/previously") — reviewer-talk that rots once merged; a comment must read true to someone who never saw the diff. Issue refs belong in TODOs only.
- Flag the same rationale across 2+ comments (per codebase, not per file) → keep one and point to it; recurring across files → an ADR or canonical doc the comments cite.
- Flag an unreferenced export / unused import / unreachable branch / commented-out code → remove.

## Docs & method

- Flag a third-party API used from memory instead of verified against official docs.
- Flag a perf claim or optimization with no measurement.
- Flag a behavior change leaving touched docs/comments/README/`CLAUDE.md` stale. Materiality: don't flag on internal refactors, dep bumps, CSS-only.
- Flag a change contradicting a stated domain model or invariant.
- Flag a violation of the repo's `CLAUDE.md` / `AGENTS.md` (commit format, layout, banned imports, naming) — outranks this list.
- Flag broad cleanup mixed with behavioral change in one commit; unintended files committed.

## Detection-only — what review adds

These have no author-side mirror: you can't pre-empt finding a bug you didn't write.

- **Correctness**: bugs, missed edges, races (with a concrete interleaving), logic gaps, off-by-one; the permission/role correct for the operation. Don't flag null checks on type-proven non-null, or edges the calling contract already prevents — read the call sites first.
- **Completeness**: what is MISSING versus the intent — an unhandled case, an absent error path, a symmetric counterpart (`encode` without `decode`, `create` without `delete`).
- **Security taint-trace**: untrusted input → dangerous sink (injection / XSS / SSRF / path traversal / deserialization); authorization on every state-changing op; IDOR (ownership enforced in the query, not just the UI). For a deeper pass, use `backend.md` / `frontend.md`.
