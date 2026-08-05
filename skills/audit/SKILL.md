---
name: audit
description: "Use when auditing, reviewing, hardening, or assessing code — a whole codebase, a single module, a branch diff, staged changes, or a PR. Triggers include 'audit', 'code review', 'security review', 'find vulnerabilities', 'check for weaknesses', 'is this code safe', 'pre-release review', 'what could go wrong here', 'quality review', 'this is getting messy', 'improve the architecture', 'find refactoring opportunities', 'is this abstraction worth it'. Any language, any layer: backend, frontend, CLI, library, infrastructure-as-code. Defensive purpose only: findings and remediation, never exploit development."
---

# Audit

Auditing a target end to end — security, correctness, coherence, code quality, and architecture. The output is a structured findings report with evidence, severity, and remediation. This is defensive work: identify and explain weaknesses so they can be fixed.

## Ground rules

- **Read-only by default.** The audit never modifies the target unless the user explicitly asks for fixes afterward. Auditing and fixing are two separate mandates.
- **Evidence or it doesn't exist.** Every finding cites `file:line` (or a commit/config path) and quotes the minimal relevant snippet. No finding is reported from assumption — verify in the actual code.
- **No exploit development.** Describe the weakness, its impact, and the fix. Do not write working exploits, payloads, or attack tooling. A proof-of-concept is limited to the minimum needed to demonstrate the flaw exists (e.g. a failing test), not to weaponize it.
- **Grade honestly.** Distinguish confirmed vulnerabilities from suspicions needing verification. Say "I could not verify X" rather than inflating or burying uncertainty.
- **Conviction over volume.** Prefer a small number of high-conviction findings over a long list of nits. A report nobody finishes reading has failed regardless of what is in it.
- **The repo outranks this skill.** A finding that contradicts the target's `CLAUDE.md` / `AGENTS.md` or an established, deliberate repo pattern is not a finding — defer to the repo, or raise it as a question about the convention itself.
- **Scale to the target.** A 2,000-line service gets a full pass; a 500k-line monorepo gets a scoped audit — agree on scope with the user first (Phase 0).

Report prose is written in French, like every other message to the user; `file:line` citations, identifiers, and quoted code stay verbatim. Findings promoted to issues or PRs are written in English (see the `dev-methodology` skill).

## Pick the mode before starting

| Target | Mode | Run |
|---|---|---|
| A whole codebase, a service, a module — "audit this", "is this safe", "pre-release review" | **Full audit** | Phases 0 → 7, then Report |
| A branch diff, staged changes, a PR — "review this change", "what do you think of this diff" | **Change review** | Phase 0 (brief), then `references/review-checklist.md` + `references/code-quality.md`, then Report |
| "This module is a mess", "find refactoring opportunities", "improve the architecture" | **Architecture pass** | Phase 0 (brief), then `references/architecture.md`, then Report |

Announce the mode in one line and proceed. When the request spans two modes, run the wider one and say so.

## Phase 0 — Scoping

Before reading any code, establish with the user:

1. **Target and perimeter:** which repos/modules/directories are in scope, which are explicitly out.
2. **Context:** what the system does, who its users are, what data it handles (personal data, credentials, financial, health), what its trust boundaries are (internet-facing? internal? multi-tenant?).
3. **Priorities:** security-first, quality-first, or both equally. Any known pain points or past incidents.
4. **Depth:** quick pass (hours), standard audit, or exhaustive review. Propose a scope matching the target's size and let the user confirm.

If the user says "audit everything, any context", still state the perimeter you inferred and the depth you're applying before starting.

## Phase 1 — Reconnaissance and mapping

Build the mental model before judging anything:

- Inventory the stack: languages, frameworks, build system, entry points (HTTP routes, CLI commands, message consumers, cron jobs, event handlers).
- Map the architecture: layers, seams, data flow from every untrusted input to every sensitive sink (DB, filesystem, shell, network, DOM).
- Identify the crown jewels: authentication, authorization, session/token handling, secrets, payment paths, personal-data flows.
- Read the configuration surface: env vars, config files, CI/CD pipelines, Dockerfiles, IaC — misconfiguration is a finding class of its own.
- Note the test topology: what is covered, what is conspicuously not. Untested critical paths are pre-findings.

Produce a short written map (components, entry points, trust boundaries) and validate it with the user if anything is ambiguous.

## Phase 2 — Dependency and supply-chain audit

- Enumerate direct and transitive dependencies; check for known CVEs with the ecosystem's audit tooling (`cargo audit`, `npm audit`, `pip-audit`, `govulncheck`, etc.) when available in the environment.
- Flag: unmaintained packages, packages with install scripts, version pinning absent or too loose, lockfile missing or not committed, dependencies duplicating stdlib functionality.
- Check CI/CD: secrets exposure in logs or workflows, untrusted actions/images, missing integrity pinning (action SHAs, image digests).

## Phase 3 — Security review by category

Work through the relevant checklist systematically — do not freestyle. Load the reference matching the target:

- **Backend / API / services:** read `references/backend.md`
- **Frontend / web client / mobile web:** read `references/frontend.md`
- Full-stack targets: read both.

For each category in the checklist, either report findings or explicitly mark the category as reviewed-clean. Silence is not clearance.

## Phase 4 — Consistency and coherence review

Vulnerabilities hide in inconsistency. Look for places where the codebase disagrees with itself:

- **Divergent implementations of the same concern:** two validation paths for the same input, three ways of checking permissions, duplicated business rules that have drifted apart. The least-maintained copy is where the bug lives.
- **Seam violations:** domain logic importing infrastructure, validation done in some handlers but not others, conversions performed inconsistently at the edges.
- **Error-handling asymmetry:** some paths propagate errors, others swallow them; inconsistent behavior on the same failure class.
- **Naming and contract drift:** functions whose names promise something the code no longer does; comments and docs contradicting behavior; API responses whose shape varies by code path.
- **Configuration incoherence:** defaults differing between environments, feature flags with dead branches, timeouts/limits inconsistent across similar clients.

## Phase 5 — Behavioral pre-mortem (anticipating incoherent behavior)

Actively hunt for latent bugs that have not manifested yet. For each critical path, ask "how does this behave when…":

- **Concurrency:** two requests hit this simultaneously; a retry lands after the original succeeded; state is read-modify-written without atomicity; locks are taken in different orders.
- **Partial failure:** the third of five side effects fails — is the system left coherent? Are operations idempotent where retried? Are transactions actually covering what they must?
- **Boundary values:** empty collections, zero, negative, maximum sizes, unicode, timezone edges, leap days, clock skew, expired-but-cached credentials.
- **Ordering and time:** events arriving out of order, webhooks delivered twice, TTLs expiring mid-operation, migrations running against live traffic.
- **State-machine holes:** unreachable states that are representable, transitions with no guard, enums matched non-exhaustively.
- **Resource exhaustion:** unbounded queues, unpaginated queries, missing timeouts, connection/file-descriptor leaks on error paths.

Each pre-mortem finding must describe the concrete scenario that triggers it, not just the pattern.

## Phase 6 — Code quality and maintainability

A codebase that is hard to reason about is a codebase where the next vulnerability will hide. Read `references/code-quality.md` and apply it: abstraction quality, spaghetti growth, file sprawl, type and seam cleanliness, canonical-layer discipline.

Be ambitious here rather than cosmetic — the reference exists to push for restructurings that delete whole categories of complexity, not for rename suggestions.

## Phase 7 — Architecture depth

Read `references/architecture.md`. Surface **deepening opportunities**: shallow modules whose interface is nearly as complex as their implementation, pass-through layers that fail the deletion test, seams in the wrong place, logic that is untestable through its current interface.

Skip this phase for a narrow security-only mandate; run it whenever the user asked about structure, refactoring, testability, or maintainability.

## Report

Deliver a single structured report:

1. **Executive summary:** overall posture in a short paragraph, top 3–5 risks.
2. **Findings**, each with: unique ID, title, severity (Critical / High / Medium / Low / Info), category (security / correctness / coherence / quality / architecture), location (`file:line`), evidence snippet, impact, concrete remediation, and confidence (confirmed / probable / needs verification).
3. **Severity grading:** Critical = exploitable now with severe impact (auth bypass, RCE, data exposure); High = exploitable with conditions or severe correctness flaw; Medium = weakness requiring circumstances or defense-in-depth gap; Low = hardening opportunity; Info = observation.
4. **Reviewed-clean list:** categories examined without findings, so coverage is auditable.
5. **Prioritized remediation plan:** ordered by risk-to-effort, distinguishing quick wins from structural work.

Do not propose fixes inline in the code during the audit; the report comes first, remediation is a separate step the user decides on (per the `dev-methodology` skill: announce, then act).

## Reference files

Read each at most once per audit; each is self-contained.

- `references/backend.md` — security checklist, backend / API / services (Phase 3).
- `references/frontend.md` — security checklist, web and mobile-web clients (Phase 3).
- `references/code-quality.md` — strict maintainability and abstraction review (Phase 6, and Change-review mode).
- `references/review-checklist.md` — per-diff review checklist (Change-review mode).
- `references/architecture.md` — module depth, seams, deepening opportunities (Phase 7, and Architecture-pass mode).
