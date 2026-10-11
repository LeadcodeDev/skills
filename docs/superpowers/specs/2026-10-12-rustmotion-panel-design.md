# `baptistep:rustmotion` — persona panel for rustmotion videos

Date: 2026-10-12
Status: approved in conversation, pending written review

## Goal

Produce a rustmotion video that lands with several audiences at once. A panel of
persona sub-agents, each with its own point of view (business decision maker,
general public, technical profile), reviews every generated version, debates the
others' reviews, and the agreed corrections are applied before the next round.
The user steers at three fixed points.

Success: the final video has gone through at least one debated review round, every
version shown to the panel passed `rustmotion validate`, and the user approved the
storyboard, each round's synthesis, and the final render.

## Non-goals

- No change to the rustmotion engine. Everything the skill needs already exists
  (`validate`, `sheet`, `render`, `icons`).
- No copy of the rustmotion authoring guide inside the plugin.
- No fully autonomous mode. The three gates are mandatory.

## Location

Plugin `baptistep` (repo `LeadcodeDev/skills`), skill directory `skills/rustmotion/`,
invoked as `baptistep:rustmotion`. Usable from any directory, not only inside the
rustmotion repository.

```
skills/rustmotion/
├── SKILL.md
├── references/
│   ├── personas/
│   │   ├── decision-maker.md
│   │   ├── general-public.md
│   │   └── tech.md
│   ├── persona-format.md
│   ├── review-prompt.md
│   ├── rebuttal-prompt.md
│   └── synthesis-format.md
└── evals/
    └── evals.json
```

## Prerequisites resolved at start

1. **Working directory.** The skill always asks, with exactly two options:
   - create `./<slug>/` inside the current directory;
   - use the current directory as is.
2. **Binary.** First `target/release/rustmotion` of a local rustmotion clone, then
   `rustmotion` on `PATH`. The chosen binary must list `sheet` in `--help`;
   otherwise the skill stops and prints the build command
   (`cargo build --release` in the clone, or `cargo install --git
   https://github.com/LeadcodeDev/rustmotion`).
3. **Authoring knowledge.** The orchestrator loads the official guide
   `crates/rustmotion/skills/SKILL.md` and the `rules/` it needs (`.claude/skills/rustmotion` in the rustmotion repo is a symlink to it, which raw GitHub does not follow), from the first
   source found:
   - a local rustmotion clone (current directory or its parents, then
     `~/Development/rustmotion`);
   - raw GitHub, `LeadcodeDev/rustmotion` branch `main`.
   Single source of truth: the plugin never duplicates it.

## Working-directory layout

```
<workdir>/
├── brief.md
├── storyboard.md
├── round-1/
│   ├── scenario.json
│   ├── sheet.png
│   ├── reviews/
│   │   ├── decision-maker.pass1.md
│   │   ├── decision-maker.pass2.md
│   │   └── ...
│   └── synthesis.md
├── round-2/ ...
└── final.mp4
```

Each round is self-contained so the user can reread any of them.

## Flow

1. **Brief.** Collect: goal, primary audience, format (resolution), target
   duration, key message, active personas. Personas: the three built-in ones by
   default; the user may remove any, or add one either as a file dropped in
   `references/personas/` or described in the brief ("add an investor"), in which
   case the orchestrator writes it to `<workdir>/personas/<name>.md` using the
   persona format below. Written to `brief.md`.
2. **Storyboard.** Text only: scenes, duration per scene, message per scene, the
   visual idea per scene. Written to `storyboard.md`.
   **Gate 1** — the user approves or edits the storyboard. Nothing is generated
   before approval.
3. **Generate version N.** Scenario JSON following the authoring guide, then
   `rustmotion validate -f scenario.json` (schema + geometry) until both passes are
   green. Missing icons: `rustmotion icons check`, then propose `icons prefetch`;
   never a silent network fetch.
4. **Contact sheet.** `rustmotion sheet -f scenario.json --at <t1,...,t16>
   --cell-width 480 -o sheet.png`, with `t_i = (i + 0.5) × duration / 16`: the
   middle of 16 equal slices, a 4×4 grid at the default `--columns 4`. Measured
   on `examples/rustmotion-promo.json`: `--every duration/16` gives 17 cells with
   an empty frame 0, and 320 px cells make code unreadable.
5. **Debate.**
   - Pass 1: one sub-agent per persona, in parallel, via the Agent tool. Input:
     its persona file, `brief.md`, `storyboard.md`, `sheet.png`. Not the JSON: a
     persona judges as a viewer. Output in the pass-1 format.
   - Pass 2: one sub-agent per persona, in parallel. Input: its own pass-1 review
     and every other persona's pass-1 review. Output in the pass-2 format.
6. **Synthesis** by the orchestrator, into `synthesis.md`.
   **Gate 2** — the user sees the sheet and the synthesis and answers: apply,
   apply with edits, or stop.
7. **Apply** the approved corrections to produce version N+1 in `round-N+1/`, then
   back to step 3.
8. **Stop condition**, first one met: the user says stop at Gate 2; every persona's
   pass-2 score is ≥ 8/10; three rounds done (overridable in the brief).
9. **Final render.** `rustmotion render -f round-N/scenario.json -o final.mp4`.
   **Gate 3** — the user approves the result.

## Persona format

Each `references/personas/<name>.md` follows `references/persona-format.md`:

- who the persona is, in two or three sentences;
- what they look for in the first ten seconds;
- what makes them drop off;
- three to five scoring criteria, each scored out of 10;
- the tone of their critique.

Built-in personas:

| File | Looks for |
|---|---|
| `decision-maker.md` | business value, credibility, a clear call to action |
| `general-public.md` | understanding without jargon, emotion, pace |
| `tech.md` | accuracy, concrete detail, no hand-waving |

## Pass-1 output (strict)

```yaml
persona: decision-maker
score: 7
criteria:
  - name: business value
    score: 6
keeps:
  - at: "2.4s"
    what: "..."
changes:
  - at: "4.5s"
    issue: "..."
    fix: "..."
    priority: must   # must | should
```

`at` refers to a timestamp printed on the contact sheet.

## Pass-2 output (strict)

```yaml
persona: decision-maker
responses:
  - to: tech
    change_at: "4.5s"
    stance: agree     # agree | concede | contest
    argument: "..."
revised_score: 8
```

## Synthesis format

- **Consensus:** every `must` raised by one persona and not contested, or contested
  then conceded. Applied by default.
- **Disagreements:** each contested point with both positions side by side and the
  orchestrator's recommendation, decided in favour of the brief's primary
  audience. The user decides at Gate 2.
- **Scores:** pass-1 and pass-2 score per persona.
- **Planned changes:** the ordered list the orchestrator will apply.

## Error handling

| Situation | Behaviour |
|---|---|
| `validate` fails | fix and revalidate before any sheet; after 3 consecutive failures, stop and show the report |
| `sheet` missing from the binary | stop, print the build command |
| icon or Google font not cached | propose `icons prefetch` or `--allow-remote-fonts`; never fetch silently |
| persona output not in the strict format | relaunch it once; if still invalid, drop it for this round and say so in the synthesis |
| authoring guide unreachable (no clone, no network) | stop and say which two sources were tried |

## Evals

`evals/evals.json`, same shape as `skills/writing/evals/evals.json`. Three
prompts: a product launch for decision makers, a technical explainer, a
general-public promo. Assertions checked:

- the working-directory question offers exactly the two options;
- Gate 1 happens before any JSON is written;
- every `round-N/scenario.json` passes `rustmotion validate`;
- `sheet.png` exists in every round;
- pass-1 and pass-2 reviews exist for every active persona and match the strict
  formats;
- the synthesis separates consensus from disagreements;
- Gate 2 happens after every synthesis and Gate 3 after the final render.

## Open points

None.
