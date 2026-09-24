# Git Workflow Rules — L branch topology

Read this file when a feature is sized **L** and needs a multi-workstream branch plan. Routine Git rules (commit convention, AI-attribution ban, permissions, branch naming) live inline in the core SKILL.md under "Git essentials" — do not reload this file for ordinary branches, commits, or PRs. Read it at most once per session; re-read only if it was edited.

## Core vocabulary

- **Default branch**: the repository's default branch, usually `main`. All work ultimately lands here.
- **Feature branch**: a branch carrying one coherent change, pulled from its target branch.
- **Workstream branch** (*chantier*): an integration branch for a feature large enough to be split into several sub-features, each landing into it via its own feature branch.

## Single-workstream (S/M) recap

Feature branch pulled from the default branch; work committed there; one PR back to the default branch. Announce this in the opening announcement and start working in the same turn — no validation wait (see core methodology). Nothing else in this file applies to S/M.

## Multi-workstream (L) branch plan — requires validation

- Pull a **workstream branch** (*chantier* branch) from the default branch. It serves as the integration branch for the whole feature.
- For each sub-feature: pull a feature branch **from the workstream branch** — not from the default branch. Branching from default guarantees conflicts and stale bases once sibling sub-features touch adjacent code.
- If the workstream branch advances while a feature branch is in flight, rebase the feature branch onto it before opening or merging its PR.
- Feature branches land into the workstream branch using the feature-merge strategy recorded at kickoff.
- The workstream branch lands on the default branch as a single final PR, using the workstream-merge strategy recorded at kickoff.

Present the L plan as a proposal and **wait for validation** before creating any branch. An L topology is expensive to unwind once sibling branches exist — that is why it is the one branch plan that keeps a blocking gate.

## Applying kickoff preferences

Permissions and merge strategies are answered once at project kickoff and stored in `.claude/dev-methodology.local.md` (see core methodology). Before any commit, PR creation, or merge:

1. If the preferences file answers the question, act accordingly without re-asking.
2. If the file is missing or silent on the operation at hand, ask — as part of a consolidated batch, never one question at a time — and record the answer in the file.
3. If a permission is human-only, prepare everything up to that boundary (staged changes, PR description drafted, review summary written) and hand off to the human.
4. Re-ask only when scope changes (e.g. moving from a feature branch to the default branch) or when the user revokes an answer.

## Tracking

Tracking preferences (milestone, issues, parent chantier issue with sub-issues) also come from kickoff. Never create milestones or issues without a recorded or explicit yes. For L features, the parent chantier issue doubles as the workstream state artifact (see `references/orchestration.md`).

## Standing rules (canonical copy in SKILL.md "Git essentials")

Duplicated here verbatim so this file stands alone when handed to a sub-agent:

- **NEVER cite Claude as author or co-author.** No `Co-Authored-By: Claude` trailer, no "Generated with Claude" line, no AI attribution of any kind in commit messages, PR descriptions, or issue bodies.
- **Issues and PRs are written in English.** Titles, bodies, and comments on issues and pull requests are in English, whatever language the session conversation is in.
- **Commit convention:** `<type>(<subject>): <verb><message>` — conventional-commit type, subject = module or bounded context, imperative lowercase verb. Every commit, no exception.
- Never commit directly to the default branch.
- One commit = one logical change, message explaining the *why*.
- Before opening a PR, re-read the full diff with hostile-stranger eyes; the PR description states the why, the scope, and what is explicitly out of scope.
- **Issues and PRs open complete, never bare.** Reviewers, assignee, and labels are set in the creation command itself, not added afterwards. Assignee: whoever will do the work. Reviewers: from `.claude/dev-methodology.local.md`, with `CODEOWNERS` winning for the paths it covers — never guess, requesting review notifies a human. Labels: only labels the repository already has; propose a new one separately rather than creating it as a side effect.
- **Every PR references its issue.** `Closes #N` when it fully resolves it, `Refs #N` when it advances it. If no issue exists, state that in the body rather than leaving the link silently absent.
- **The PR description is part of the diff.** Before pushing to an open PR, re-read its description and rewrite it in the same turn if the commit invalidates anything it claims. A description that lags is incomplete; one still promising what the branch has removed actively misleads the reviewer.
- **Record in the description what the session knows and the diff does not** — why an approach was abandoned, what an evaluation measured, which alternative was rejected and on what evidence. The branch keeps the code; the conversation that justified it does not survive.
- **The diff is the ceiling on that record.** What the session learned past these changes — an audit that turned up other defects, work deferred to a later layer — goes in an issue the description links to, not in a section of the body.
- A PR that cannot be reviewed in one sitting is a sign the workstream decomposition was wrong — flag it rather than pushing through.
- **How it reads.** Everything published is signed with a human's name and read as that person's work. Bold is not a heading. Never announce a count ("two departures", "three things this leaves open"). Never narrate the work or the session's circumstances — a tool that did not answer, CI that does not fire on a stacked PR, how the base branch moved: the reader needs the consequence, never the story. Report a result (`1278 passed, 0 failed`), not a pasted transcript. One em dash per paragraph at most, no closing summary, and never grade a finding or thank a reviewer. Calibrate on the three most recent comments a human maintainer wrote in this repository.
- **Answering a review.** The thread exists to be closed. Push the fix; `Fixed in <sha>.` is a complete reply. One reply per thread, answering what was asked. A disagreement is two sentences. Anything the reply turns up beyond the thread becomes an issue the reply links to. Name in one sentence what you could not verify. A person gets an answer, a bot gets an outcome.
