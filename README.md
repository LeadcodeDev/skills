# baptistep

Engineering skills for [Claude Code](https://claude.com/claude-code) — how a codebase is assessed, how what you learn gets published, and how a video gets reviewed before it ships.

Three skills, one plugin. They are opinionated on purpose: each one encodes decisions that would otherwise be re-litigated in every session.

## Install

```
/plugin marketplace add LeadcodeDev/skills
/plugin install baptistep@baptistep-skills
```

Then the skills are available as `/baptistep:audit`, `/baptistep:writing` and `/baptistep:rustmotion`. Most of the time you will not type them — each declares the contexts it should fire in, and Claude consults them on its own.

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

### `rustmotion` — a video reviewed by the people it is for

A [rustmotion](https://github.com/LeadcodeDev/rustmotion) video generated, then refined by a panel of personas: a business decision maker, the general public, a technical profile, plus any audience you add for one video. Each reviews a contact sheet of the render, then argues with the others' reviews, and the agreed corrections become the next round.

- **Three gates.** You approve the storyboard, the synthesis of every round, and the final render. Nothing is generated before the storyboard is approved.
- **Personas are real agents.** `panel-decision-maker`, `panel-general-public` and `panel-tech` ship in `agents/`, each pinned to `sonnet` with only `Read` and `Write`. A persona added for one video runs on a generic agent, because Claude Code only loads agents at session start.
- **Pass 1 is blind.** Personas review in parallel without seeing each other; the debate happens in pass 2, where each one agrees, concedes or contests every change the others asked for.
- **Viewers, not reviewers of code.** Personas see the brief, the storyboard and a 4×4 contact sheet from `rustmotion sheet`, never the JSON.
- **Nothing unvalidated reaches the panel.** Every round passes `rustmotion validate` first.
- **Disagreements are decided by the primary audience** named in the brief, and shown to you side by side.

Works from any directory: it asks whether to create a folder or use the current one, and loads the authoring guide from a local rustmotion clone or from GitHub rather than carrying a copy that would drift.

## How they fit together

`audit` tells you what you are dealing with before you change it, and its findings become the issues the work is tracked against afterwards. `writing` turns what you learned into something publishable, on the blog or on LinkedIn, and treats your own commits and measurements as the primary source they are.

They share one bias: prefer the artifact that cannot lie. Types over comments, tests over intentions, a compiler-enforced boundary over a rule written in Markdown, a reproduced measurement over a quoted one.

## Language

Skill instructions, commit messages, issues and pull requests are in English. `audit` writes its report in French; that is a preference of this toolbox, not a requirement of the skills, and it is one line to change.

## Contributing

Changes go through a branch and a pull request. Run `claude plugin validate .` before pushing, and expect the description of a skill to matter as much as its body: it is the only part always in context, and it is what decides whether the skill fires at all.
