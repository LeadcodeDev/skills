# Code quality and maintainability

Read this for Phase 6 of a full audit, and for every Change-review. An unusually strict pass on implementation quality, maintainability, abstraction quality, and codebase health.

**Be ambitious about structure.** Do not stop at local cleanup opportunities. Actively search for *code-judo* moves: restructurings that preserve behavior while making the implementation dramatically simpler, smaller, more direct. Prefer the solution that makes the code feel inevitable in hindsight. If there is a path to delete complexity rather than rearrange it, push hard for that path.

## Stance

> Rethink how the code could be structured to meaningfully improve quality without changing behavior. Improve abstractions and modularity, reduce spaghetti, improve succinctness and legibility. If there is a clear path to a better implementation that involves restructuring part of the codebase, say so. Be thorough and rigorous — measure twice, cut once.

## Non-negotiable standards

1. **Do not let a change push a file from under 1k lines to over 1k lines without a very strong reason.**
   - Treat this as a strong smell by default. (Adjust the threshold to the repo's own norm — many repos sit closer to ~500 lines.)
   - Prefer extracting helpers, subcomponents, modules, or local abstractions instead of letting a file sprawl.
   - If the diff crosses the threshold, explicitly ask whether the code should be decomposed first.
   - Waive only when there is a compelling structural reason *and* the resulting file is still clearly organized.

2. **Do not allow random spaghetti growth in existing code.**
   - Be highly suspicious of new ad-hoc conditionals, scattered special cases, or one-off branches inserted into unrelated flows.
   - "Weird if statements in random places" is a design problem, not a stylistic nit.
   - Prefer pushing the logic into a dedicated abstraction, helper, state machine, policy object, or separate module instead of tangling an existing path.
   - Call out changes that make surrounding code harder to reason about, even when they technically work.

3. **Bias toward cleaning the design, not just accepting working code.**
   - If behavior can stay the same while the structure becomes meaningfully cleaner, push for the cleaner version.
   - Do not rubber-stamp "it works" implementations that leave the codebase messier.
   - Strongly prefer simplifications that remove moving pieces over refactors that spread the same complexity around.

4. **Prefer direct, boring, maintainable code over hacky or magical code.**
   - Treat brittle, ad-hoc, or "magic" behavior as a quality problem.
   - Be skeptical of generic mechanisms that hide simple data-shape assumptions.
   - Flag thin abstractions, identity wrappers, and pass-through helpers that add indirection without buying clarity.

5. **Push hard on type and seam cleanliness when they affect maintainability.**
   - Question unnecessary optionality, `unknown`, `any`, or cast-heavy code when a clearer type boundary could exist.
   - Prefer explicit typed models or shared contracts over loosely-shaped ad-hoc objects.
   - If a branch relies on a silent fallback to paper over an unclear invariant, ask whether the seam should be made explicit instead.

6. **Keep logic in the canonical layer and reuse existing helpers.**
   - Call out feature logic leaking into shared paths, and implementation details leaking through interfaces.
   - Prefer existing canonical utilities over bespoke one-offs.
   - Push code toward the right package, service, or module instead of normalizing architectural drift.

7. **Treat unnecessary sequential orchestration and non-atomic updates as design smells when the cleaner structure is obvious.**
   - If independent work is serialized for no good reason, ask whether the flow should run concurrently.
   - If related updates can leave state half-applied, push for a more atomic structure.
   - Do not over-index on micro-optimizations, but do flag avoidable orchestration complexity that makes the implementation brittle.

## Primary review questions

For every meaningful change or module:

- Is there a code-judo move that would make this dramatically simpler?
- Can this be reframed so fewer concepts, branches, or helper layers are needed?
- Does this improve or worsen the local architecture?
- Was branching complexity added where a better abstraction should exist?
- Did a previously cohesive module become more coupled, more stateful, or harder to scan?
- Is this logic living in the right file and layer?
- Did this enlarge a file or component past a healthy size boundary?
- Are there repeated conditionals that signal a missing model or missing helper?
- Is the implementation direct and legible, or does it rely on special cases and incidental control flow?
- Is this abstraction earning its keep, or is it just a wrapper?
- Were casts, optionality, or ad-hoc object shapes introduced that obscure the real invariant?
- Is this orchestration more sequential or less atomic than it needs to be?

## Flag aggressively

- A complicated implementation where a cleaner reframing could delete whole categories of complexity.
- Refactors that move code around but fail to reduce the number of concepts a reader must hold in their head.
- A file crossing the repo's size norm, especially when the new code could be split out.
- New conditionals bolted onto unrelated code paths.
- One-off booleans, nullable modes, or flags that complicate existing control flow.
- Feature-specific logic leaking into general-purpose modules.
- Generic "magic" handling that hides simple structure.
- Thin wrappers or identity abstractions that add indirection without simplifying anything.
- Unnecessary casts, `any`, `unknown`, or optional params that muddy the real contract.
- Copy-pasted logic instead of extracted helpers.
- Narrow edge-case handling implemented in the middle of an already busy function.
- Refactors that pass tests but make the code less modular or less readable.
- "Temporary" branching that is likely to become permanent debt.
- Bespoke helpers where the codebase already has a canonical utility.
- Logic added in the wrong layer/package when a clear canonical home exists.
- Sequential async flow where obviously independent work would be simpler run concurrently.
- Partial-update logic that leaves state less atomic than necessary.

## Preferred remedies

- Delete a whole layer of indirection rather than polishing it.
- Reframe the state model so conditionals disappear instead of getting centralized.
- Change the ownership seam so the feature becomes a natural extension of an existing abstraction.
- Turn special-case logic into a simpler default flow with fewer exceptions.
- Extract a helper or pure function.
- Split a large file into smaller focused modules.
- Move feature-specific logic behind a dedicated abstraction.
- Replace condition chains with a typed model or explicit dispatcher.
- Separate orchestration from business logic.
- Collapse duplicate branches into a single clearer flow.
- Delete wrappers that do not meaningfully clarify the interface.
- Reuse the existing canonical helper instead of introducing a near-duplicate.
- Make type seams explicit so the control flow gets simpler.
- Move the logic to the module that already owns the concept.
- Parallelize independent work when that also simplifies the orchestration.
- Restructure related updates into a more atomic flow when partial state would be harder to reason about.

Do not settle for "maybe rename this" when the real issue is structural. Do not settle for a cleaner version of the same messy idea when there is a plausible path to a much simpler idea.

## Tone

Direct, serious, demanding about quality. Not rude — but do not soften a major maintainability issue into a mild suggestion. If the code makes the codebase messier, say so clearly. If the implementation missed an opportunity for a dramatic simplification, say that clearly too.

Useful phrasings for PR comments (English, per the repo-artifact rule):

- `this pushes the file past 1k lines. can we decompose this first?`
- `this adds another special-case branch into an already busy flow. can we move this behind its own abstraction?`
- `this works, but it makes the surrounding code more spaghetti. let's keep the behavior and restructure the implementation.`
- `this feels like feature logic leaking into a shared path. can we isolate it?`
- `this abstraction seems unnecessary. can we just keep the direct flow?`
- `why does this need a cast / optional here? can we make the seam more explicit instead?`
- `this looks like a bespoke helper for something we already have elsewhere. can we reuse the canonical one?`
- `i think there's a code-judo move here that makes this much simpler. can we reframe this so these branches disappear?`
- `this refactor moves complexity around, but doesn't really delete it. is there a way to make the model itself simpler?`

## Ordering findings

1. Structural quality regressions
2. Missed opportunities for dramatic simplification / code-judo restructuring
3. Spaghetti and branching-complexity increases
4. Seam, abstraction, and type-contract problems that make the code harder to reason about
5. File-size and decomposition concerns
6. Modularity and abstraction issues
7. Legibility and maintainability concerns

Do not flood the report with low-value nits when larger structural issues exist.

## Approval bar (Change-review mode)

Correct behavior is not sufficient. The bar is:

- no clear structural regression
- no obvious missed opportunity to make the implementation dramatically simpler when such a path is visible
- no unjustified file-size explosion
- no obvious spaghetti growth from special-case branching
- no hacky or magical abstraction that makes the code harder to reason about
- no unnecessary wrapper/cast/optionality churn obscuring the real design
- no clear seam leak or avoidable canonical-helper duplication
- no missed obvious decomposition that would materially improve maintainability

Treat these as presumptive blockers unless the author justifies them clearly:

- the change preserves a lot of incidental complexity when a plausible code-judo move would delete it
- the change pushes a file past the repo's size norm
- the change adds ad-hoc branching that tangles an existing flow
- the change solves a local problem by scattering feature checks across shared code
- the change adds an unnecessary abstraction, wrapper, or cast-heavy contract
- the change duplicates an existing helper, or puts logic in the wrong layer when a canonical home exists

If those conditions are not met, leave explicit, actionable feedback and push for a cleaner decomposition.
