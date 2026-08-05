# Architecture depth

Read this for Phase 7 of a full audit, and for an Architecture-pass. The goal is to surface architectural friction and propose **deepening opportunities** — restructurings that turn shallow modules into deep ones. The payoff is testability, locality, and a codebase a stranger (or an agent) can navigate.

## Vocabulary — use these terms exactly

Consistent language is the point. Don't drift into "component", "service", "API", or "boundary".

**Module** — anything with an interface and an implementation. Deliberately scale-agnostic: a function, a class, a package, a tier-spanning slice. *Avoid*: unit, component, service.

**Interface** — everything a caller must know to use the module correctly. The type signature, but also invariants, ordering constraints, error modes, required configuration, performance characteristics. *Avoid*: API, signature (too narrow — those are only the type-level surface).

**Implementation** — what is inside a module. Distinct from **adapter**: a thing can be a small adapter with a large implementation (a Postgres repo) or a large adapter with a small implementation (an in-memory fake). Reach for "adapter" when the seam is the topic, "implementation" otherwise.

**Depth** — leverage at the interface: how much behavior a caller or test can exercise per unit of interface it must learn. **Deep** = a lot of behavior behind a small interface. **Shallow** = the interface is nearly as complex as the implementation.

**Seam** *(Michael Feathers)* — a place where behavior can be altered without editing in that place; the *location* at which a module's interface lives. Where to put the seam is its own design decision, distinct from what goes behind it. *Avoid*: boundary (overloaded with DDD's bounded context).

**Adapter** — a concrete thing satisfying an interface at a seam. Describes *role* (which slot it fills), not substance.

**Leverage** — what callers get from depth: more capability per unit of interface learned. One implementation pays back across N call sites and M tests.

**Locality** — what maintainers get from depth: change, bugs, knowledge, and verification concentrate in one place instead of spreading across callers. Fix once, fixed everywhere.

## Principles

- **Depth is a property of the interface, not the implementation.** A deep module can be internally composed of small, swappable parts — they just aren't part of the interface. A module can have **internal seams** (private to its implementation, used by its own tests) as well as the **external seam** at its interface.
- **The deletion test.** Imagine deleting the module. If complexity vanishes, it wasn't hiding anything — it was a pass-through. If complexity reappears across N callers, it was earning its keep.
- **The interface is the test surface.** Callers and tests cross the same seam. If you want to test *past* the interface, the module is probably the wrong shape.
- **One adapter means a hypothetical seam. Two adapters means a real one.** Don't introduce a seam unless something actually varies across it.

Rejected framings: depth as a ratio of implementation-lines to interface-lines (rewards padding the implementation — use depth-as-leverage); "interface" as the language keyword or a class's public methods (too narrow); "boundary" (say seam or interface).

## 1. Explore

If the repo carries a domain glossary (`CONTEXT.md` or equivalent) or ADRs under `docs/adr/`, read the ones covering the area first. The domain language gives names to good seams; ADRs record decisions this pass should not re-litigate. Neither is required — proceed without them if absent.

Then walk the codebase — dispatch `Explore` sub-agents for breadth. Don't follow rigid heuristics; explore organically and note where you experience friction:

- Where does understanding one concept require bouncing between many small modules?
- Where are modules **shallow** — interface nearly as complex as the implementation?
- Where have pure functions been extracted just for testability, while the real bugs hide in how they are called (no **locality**)?
- Where do tightly-coupled modules leak across their seams?
- Which parts are untested, or hard to test through their current interface?

Apply the **deletion test** to anything you suspect is shallow: would deleting it concentrate complexity, or just move it? "Concentrates" is the signal you want.

## 2. Present candidates

A numbered list of deepening opportunities. For each:

- **Files** — which files/modules are involved
- **Problem** — why the current architecture causes friction
- **Solution** — plain-English description of what would change
- **Benefits** — in terms of locality and leverage, and how tests would improve

Use the repo's domain vocabulary for the domain and the vocabulary above for the architecture. If the domain calls it "Order", talk about "the Order intake module" — not "the FooBarHandler", not "the Order service".

**ADR conflicts:** if a candidate contradicts an existing ADR, surface it only when the friction is real enough to warrant reopening the decision, and mark it clearly (*"contradicts ADR-0007 — but worth reopening because…"*). Don't list every theoretical refactor an ADR forbids.

Do not propose interfaces yet. Ask which candidate the user wants to explore.

## 3. Deepening — classify the dependencies

Once a candidate is picked, classify its dependencies. The category determines how the deepened module is tested across its seam.

1. **In-process** — pure computation, in-memory state, no I/O. Always deepenable: merge the modules and test through the new interface directly. No adapter needed.
2. **Local-substitutable** — dependencies with local test stand-ins (PGLite for Postgres, an in-memory filesystem). Deepenable if the stand-in exists; the deepened module is tested with the stand-in running in the suite. The seam is internal — no port at the module's external interface.
3. **Remote but owned** — your own services across a network (microservices, internal APIs). Define a **port** at the seam; the deep module owns the logic and the transport is injected as an **adapter**. Tests use an in-memory adapter, production an HTTP/gRPC/queue adapter. Recommendation shape: *"Define a port at the seam, implement an HTTP adapter for production and an in-memory adapter for testing, so the logic sits in one deep module even though it's deployed across a network."*
4. **True external** — third-party services you don't control (Stripe, Twilio). The deepened module takes the dependency as an injected port; tests provide a mock adapter.

**Seam discipline.** One adapter = hypothetical seam; two = real one. Don't introduce a port unless at least two adapters are justified (typically production + test) — a single-adapter seam is just indirection. And don't expose internal seams through the interface merely because tests use them.

**Testing strategy: replace, don't layer.** Old unit tests on the shallow modules become waste once tests exist at the deepened module's interface — delete them. Write new tests at that interface; the interface is the test surface. Assert on observable outcomes, not internal state. Tests should survive internal refactors; if a test must change when the implementation changes, it is testing past the interface.

## 4. Design the interface twice

When exploring alternative interfaces for a chosen candidate, use a parallel sub-agent pattern — the first idea is unlikely to be the best.

**Frame the problem space** first, for the user: the constraints any new interface must satisfy, the dependencies it relies on and their category, and a rough illustrative sketch to make the constraints concrete (not a proposal). Show it, then proceed immediately — the user reads while the sub-agents work.

**Spawn 3+ sub-agents in parallel**, each producing a *radically different* interface. Brief each one separately with file paths, coupling details, dependency category, and what sits behind the seam — the brief is independent of the user-facing framing. Give each a different design constraint:

- Minimize the interface — 1–3 entry points max, maximum leverage per entry point.
- Maximize flexibility — support many use cases and extension.
- Optimize for the most common caller — make the default case trivial.
- Design around ports and adapters for cross-seam dependencies (when applicable).

Include both the architecture vocabulary above and the repo's domain vocabulary in each brief so the designs name things consistently.

Each sub-agent returns: the interface (types, methods, params, plus invariants, ordering, error modes); a usage example; what the implementation hides behind the seam; the dependency strategy and adapters; and trade-offs — where leverage is high and where it is thin.

**Present and compare.** Sequentially, so each design can be absorbed, then contrast them in prose by **depth** (leverage at the interface), **locality** (where change concentrates), and **seam placement**. Close with your own recommendation — which is strongest and why. Propose a hybrid when elements combine well. Be opinionated: the user wants a strong read, not a menu.

## Recording decisions

If the user rejects a candidate for a load-bearing reason — one a future explorer would need in order not to re-suggest the same thing — offer to record it as an ADR. Skip ephemeral reasons ("not worth it right now") and self-evident ones. Likewise, if deepening names a concept absent from the repo's glossary, offer to add the term.
