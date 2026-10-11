---
name: rustmotion
description: Use when the user wants a rustmotion video made and refined by a panel of personas — a business decision maker, the general public, a technical profile, or any audience they add — who each review a contact sheet of the render, debate each other's reviews, and drive the next iteration. Triggers include "make a video with rustmotion and have it reviewed", "panel review of my video", "iterate on this video with several points of view", "fais une vidéo rustmotion avec plusieurs avis", "fais relire la vidéo par un décideur / le grand public / un profil tech", "améliore la vidéo par itérations". Works from any directory. Not for a one-shot scenario with no review loop: that is the rustmotion authoring guide on its own.
---

# Rustmotion persona panel

A video is generated, rendered as a contact sheet, reviewed by personas who then argue with each other's reviews, and corrected. The user steers at three gates. The loop exists because one author converges on their own taste; independent viewers with opposed priorities do not.

Messages to the user follow the session's language. Files written in the working directory are in English.

## Rules that do not bend

- **Three gates, always.** Gate 1 after the storyboard, Gate 2 after every synthesis, Gate 3 after the final render. Each one ends the turn and waits for the user's answer. Nothing is generated before Gate 1.
- **Nothing reaches the panel unvalidated.** Every `scenario.json` passes `rustmotion validate` (schema and geometry) before its contact sheet is rendered.
- **Personas judge as viewers.** They see the brief, the storyboard and the contact sheet. Never the JSON.
- **Pass 1 is blind.** No persona sees another's review before writing its own. Independence is the point of the panel.
- **No silent network.** A missing icon or font is proposed to the user, never fetched on its own.

## Step 0 — prerequisites

Run these before the brief, in order.

1. **Working directory.** Ask with exactly two options, nothing else:
   - create `./<slug>/` in the current directory (propose a slug from the topic);
   - use the current directory as is.
2. **Binary.** Resolve `RM`, the rustmotion command used for the whole session:
   - `target/release/rustmotion` of a local rustmotion clone (see 3 for where clones are looked for);
   - otherwise `rustmotion` on `PATH`.
   Then `$RM --help` must list `sheet`. If it does not, stop and print the fix: `cargo build --release` in the clone, or `cargo install --git https://github.com/LeadcodeDev/rustmotion`.
3. **Authoring guide.** Load `SKILL.md` of the official rustmotion guide, from the first source that exists:
   - a local clone: `crates/rustmotion/skills/SKILL.md` in the current directory or one of its parents, then in `~/Development/rustmotion`;
   - raw GitHub: `https://raw.githubusercontent.com/LeadcodeDev/rustmotion/main/crates/rustmotion/skills/SKILL.md`, and its `rules/<name>.md` beside it.
   Load the `rules/` files the guide points to when the storyboard needs them, not all of them. If neither source answers, stop and name both. Never write a scenario from memory: the guide is the single source of truth and it changes.

## Step 1 — brief

Collect, then write `brief.md`:

- **goal** — what the viewer should do or think after watching;
- **primary audience** — the persona that wins disagreements;
- **format** — 1080×1920, 1920×1080 or 1080×1080;
- **duration** — target, in seconds;
- **key message** — one sentence;
- **personas** — the three built-in agents by default: `decision-maker`, `general-public`, `tech`. The user may remove any. An added persona is either a file the user points to, or one they describe ("add an investor"): write it to `<workdir>/personas/<name>.md` following `references/persona-format.md`, and show it in the brief;
- **max rounds** — 3 unless the user says otherwise.

Ask only for what the request did not already give.

## Step 2 — storyboard, then Gate 1

Write `storyboard.md`: one entry per scene with its duration, its message, and the visual idea. Durations add up to the target.

**Gate 1.** Show the storyboard and stop. The user approves or edits it. Apply edits and show it again until approved.

## Step 3 — generate round N

1. Write `round-N/scenario.json` from the storyboard (round 1) or from the previous round's scenario plus the approved changes (round N+1).
2. `$RM validate -f round-N/scenario.json`. Fix and rerun until both passes are green. After three consecutive failures on the same round, stop and show the report.
3. If validation or render reports a missing icon, run `$RM icons check -f round-N/scenario.json` and propose `$RM icons prefetch -f round-N/scenario.json`. For a missing Google font, propose rerunning with `--allow-remote-fonts`. Wait for the user's yes.

## Step 4 — contact sheet

```bash
$RM sheet -f round-N/scenario.json --at <t1,...,t16> --cell-width 480 -o round-N/sheet.png
```

Cut the duration into 16 equal slices and take the middle of each, `t_i = (i + 0.5) × duration / 16` for `i` from 0 to 15, rounded to 0.1 s. That gives a 4×4 grid at the default four columns.

Do not use `--every duration/16`: it samples from 0 to the last frame inclusive, which gives 17 cells, and frame 0 is usually an empty fade-in. Do not keep the default 320 px cells: small text becomes unreadable at that size, and the panel would report a readability problem the video does not have.

Read the sheet yourself before dispatching: a blank or broken sheet is a generation bug to fix, not something to send to the panel.

## Step 5 — debate

Each persona is a real agent. The persona, its model and its tools live in the agent file; the briefing only carries the pass, the paths and the output format.

| Persona | `subagent_type` | Prompt |
|---|---|---|
| built-in | `baptistep:panel-decision-maker`, `baptistep:panel-general-public`, `baptistep:panel-tech` | the filled briefing |
| added for this video | `general-purpose`, with `model: sonnet` | `You are the persona described in <workdir>/personas/<name>.md. Read that file first, stay in that persona, and follow its How you work section.` then the filled briefing |

An added persona cannot be a real agent: Claude Code loads agents at session start, and one written mid-session is reported as not found. To make a persona permanent, add it to `agents/` in the plugin and restart.

The synthesis and the arbitration stay with you, on the session model.

**Pass 1.** One agent per active persona, all dispatched in the same message so they run in parallel. Build each briefing from `references/review-prompt.md`. Each writes `round-N/reviews/<persona>.pass1.md`.

**Pass 2.** Once every pass-1 file exists, one agent per persona again, in parallel, briefed from `references/rebuttal-prompt.md`. Each receives its own pass-1 review and all the others'. Each writes `round-N/reviews/<persona>.pass2.md`.

**Format check.** Parse every file as YAML against the strict format in its prompt. Compare `at` values as numbers, not strings: the sheet prints `6.10s` and personas write `6.1s` as often as `6.10s`. An invalid file is relaunched once with the parse error quoted. Still invalid: drop that persona for this round and say so in the synthesis.

**Legibility check.** The sheet shows each frame at a quarter of 1920 px, so text that is fine on screen can look unreadable on it. Before any change about text being too small, too dim or unreadable enters the synthesis, render that instant at full size and look at it yourself:

```bash
$RM still -f round-N/scenario.json --time <t> -o round-N/still-<t>.png
```

Readable at full size: drop the change and list it under **Dropped** with the still's path. Still unreadable: keep it.

## Step 6 — synthesis, then Gate 2

Write `round-N/synthesis.md` in the format of `references/synthesis-format.md`:

- **consensus** — every `must` that no one contested, or that was contested then conceded;
- **disagreements** — each contested point, both positions side by side, your recommendation decided in favour of the primary audience;
- **scores** — pass 1 and pass 2, per persona and per criterion;
- **planned changes** — the ordered list you will apply.

`should` items enter the planned changes only when uncontested and cheap.

**Gate 2.** Show the sheet path, the scores and the synthesis, then stop. The user answers: apply, apply with edits, or stop.

## Step 7 — loop or finish

- **Apply** → round N+1, back to Step 3.
- Stop when the first of these is met: the user says stop; every persona's pass-2 score is 8 or more; the max rounds are done. Say which one ended the loop.

## Step 8 — final render, then Gate 3

```bash
$RM render -f round-N/scenario.json -o final.mp4
```

**Gate 3.** Give the path of `final.mp4`, the score trajectory per persona across rounds, and what the last synthesis left unresolved. The user approves, or asks for another round, which reopens Step 3.

## Working directory

```
<workdir>/
├── brief.md
├── storyboard.md
├── personas/            # only personas added for this video
├── round-1/
│   ├── scenario.json
│   ├── sheet.png
│   ├── reviews/<persona>.pass1.md, <persona>.pass2.md
│   └── synthesis.md
├── round-2/ ...
└── final.mp4
```

A round is never overwritten. The user can reread any of them.

## Red flags

| Thought | Reality |
|---|---|
| "The storyboard is obvious, I'll generate straight away" | Gate 1 is not optional. |
| "I'll give the personas the JSON so they can be precise" | A viewer never sees the JSON. Their `at` timestamps are precise enough. |
| "I'll run the personas one by one so the next can build on the previous" | That kills pass 1. Contamination is what pass 2 is for. |
| "Scores are all 7+, the user will be fine with it" | The loop stops at Gate 2 or at the threshold, not on your impression. |
| "Validation fails on a detail, the sheet still renders" | Nothing unvalidated reaches the panel. |
| "I remember how rustmotion scenarios look" | The guide changes. Load it. |
