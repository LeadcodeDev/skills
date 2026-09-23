# baptistep

Engineering skills for [Claude Code](https://claude.com/claude-code) — how development work is conducted, how a codebase is assessed, and how what you learn gets published.

Three skills, one plugin. They are opinionated on purpose: each one encodes decisions that would otherwise be re-litigated in every session.

## Install

```
/plugin marketplace add LeadcodeDev/skills
/plugin install baptistep@baptistep-skills
```

Then the skills are available as `/baptistep:dev-methodology`, `/baptistep:audit` and `/baptistep:writing`. Most of the time you will not type them — each declares the contexts it should fire in, and Claude consults them on its own.

To work on the skills themselves, point the marketplace at a local clone instead:

```
git clone git@github.com:LeadcodeDev/skills.git
/plugin marketplace add ./skills
/plugin install baptistep@baptistep-skills
```

Validate any change before pushing:

```
claude plugin validate .
```

## The skills

### `dev-methodology` — how the work is done

Behavioural rules for conducting development work: what gets ceremony, what gets none, and where the process is allowed to stop and wait for you.

It exists because the expensive failures in an assisted session are not bad code. They are a three-line fix that triggers a full specification, a plan posted and then waited on, and a question asked twice.

- **Triage by blast radius.** Every task is an Iteration, S, M or L, and the ceremony is proportional. Most follow-up turns are Iterations, which get no re-triage, no new spec and no brainstorm.
- **Three blocking waits, and only three.** An L decomposition, a merge into the default branch, and anything irreversible or outward-facing — the last being a closed list, not a judgement call. Two other turns end on a question rather than on a request for permission, and holding those apart is what keeps the list of three closed. Everything else is announced and proceeds.
- **A question is a question.** "Should we use X?" gets answered, not implemented. This is the one place the speed principle yields: acting instead of answering does not save a round-trip, it spends one, and it spends it on a diff nobody asked for.
- **Done means done, and met along the way means fixed.** Five things asked for is five things delivered, with the specific blocker named if one is genuinely stuck. Breakage you walk past while doing something else is not a finding — reporting it moves the work back onto the reader's list. What the question itself is about stays an answer.
- **The spec is the contract.** Before it exists, questions are cheap. After it exists, it is the answer, and a question arising mid-implementation is a defect in the spec to be amended explicitly.
- **Sub-agents, never git worktrees.** Parallel work is made safe by freezing shared contracts and partitioning files, with the plumbing every feature touches owned by the orchestrator rather than fought over. Independent work is dispatched together and the main thread keeps going rather than idling on results.
- **Architectural drift gets a trigger.** Every other rule judges one change; drift is emergent, so the check fires between changes on observable signals rather than on a calendar nobody keeps.

Git conventions travel with it: conventional commits, no AI attribution anywhere, issues and pull requests opened with reviewers, assignee and labels already set.

References loaded on condition, once per session: `rust.md` for Rust or hexagonal codebases, `orchestration.md` before the first sub-agent dispatch, `git.md` only for a multi-workstream branch topology.

### `audit` — what the codebase is actually like

A read-only assessment of a target — a whole codebase, a module, a branch diff, a pull request — across security, correctness, coherence, code quality and architecture. The output is a findings report with evidence, severity and remediation.

- **Evidence or it doesn't exist.** Every finding cites `file:line` and quotes the relevant snippet. Nothing is reported from assumption.
- **Read-only by default.** Auditing and fixing are separate mandates; it does not touch the target unless you ask afterwards.
- **Conviction over volume.** A short list of high-confidence findings beats a long list of nits, because a report nobody finishes has failed regardless of its contents.
- **The repo outranks the skill.** A finding that contradicts the target's own conventions is not a finding — it is a question about the convention.
- **Defensive only.** Weaknesses are described and fixed, never weaponised.

Seven phases from scoping to architecture depth, with per-domain references for backend, frontend, architecture, code quality and review.

### `writing` — publishing what you found

Text a stranger can trust, for two platforms: long-form articles on an [Explainer](https://github.com/LeadcodeDev/explainer) blog, and LinkedIn posts. The discipline is factual rather than stylistic, because what you publish is read by people who cannot check your work.

- **Fact, inference and opinion are distinguishable.** An opinion written in the grammar of a fact borrows the authority of evidence while carrying none.
- **No invented specifics.** Numbers, dates, versions, quotes. Quotation marks are a promise of verbatim, and attribution is a claim to be verified by opening the source before writing "according to".
- **Your own work is the spine.** External sources support what your experience cannot reach; a number earns its place only if removing it changes the argument.
- **Rigour is infrastructure, not display.** All of the verification, almost none of the apparatus — disclosure belongs in the sentence that makes the claim, never in a methodology box or a closing confession.
- **The platform is settled first, not last.** It decides what counts as evidence, how much research the piece needs, and what register the prose is in. Deciding it at formatting time means discovering you wrote the wrong shape.

On the blog: source ranking, coverage without a template, inline links on the claim they support, MDX. On LinkedIn: three frameworks to pick from, hard limits on the hook and the length, no link and no hashtag in the body, and one rule that does most of the work — the only permitted target of criticism is yourself. Raw notes are full of verdicts about other people's tools, and relaying them faithfully feels like accuracy; it is how you publish a private judgement to an audience that includes its subject.

Two things are asked before a post is drafted rather than guessed: which framework, and whether lists carry emoji bullets. The second is a preference that changes per post, and the skill that decides it for you is the one that produces posts in its own voice instead of yours.

For Explainer documentation pages rather than articles, this defers to a separate docs skill.

## How they fit together

`dev-methodology` governs how any of the work happens, including the work the other two do. `audit` tells you what you are dealing with before you change it, and its findings become issues under the methodology's Git rules. `writing` turns what you learned into something publishable, on the blog or on LinkedIn, and treats your own commits and measurements as the primary source they are.

They share one bias: prefer the artifact that cannot lie. Types over comments, tests over intentions, a compiler-enforced boundary over a rule written in Markdown, a reproduced measurement over a quoted one.

## Language

Skill instructions, commit messages, issues and pull requests are in English. `dev-methodology` directs Claude to address the user in French; that is a preference of this toolbox, not a requirement of the skills, and it is one line to change.

## Contributing

Changes go through a branch and a pull request — the methodology applies to its own repository. Run `claude plugin validate .` before pushing, and expect the description of a skill to matter as much as its body: it is the only part always in context, and it is what decides whether the skill fires at all.
