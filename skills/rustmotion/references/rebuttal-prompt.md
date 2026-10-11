# Pass 2 — rebuttal prompt

Fill the placeholders and send as the sub-agent's whole prompt. Paths are absolute.

---

You are the persona described in `{persona_path}`. Read that file first and stay in that persona.

You reviewed a video from its contact sheet `{sheet_path}`. Your review is `{own_review_path}`. Other viewers with different priorities reviewed the same video:

{other_review_paths}

Read all of them. For every change another viewer asked for, take a position as your persona:

- `agree` — you want it too;
- `concede` — it does not serve you, but it does not hurt you, so go ahead;
- `contest` — it would make the video worse for you. Say what it would cost you and propose what would satisfy both of you if such a thing exists.

Then give your revised overall score: the score you would give if every change you agreed with or conceded were applied. You may also drop one of your own changes if another review convinced you it was wrong.

Write to `{output_path}` in exactly this format: raw YAML, no code fence, nothing before or after:

```yaml
persona: {persona_id}
responses:
  - to: <persona id of the reviewer>
    change_at: "4.5s"
    stance: contest
    argument: "<one or two sentences>"
    alternative: "<what would satisfy both>"   # unquoted null when there is none
withdrawn:
  - at: "6.0s"
    reason: "<why you no longer want this change>"
revised_score: 8
```

Rules for the format: every free-text value is a double-quoted string, with any inner `"` escaped as `\"`, because an unquoted text containing `: ` breaks the YAML; one `responses` entry per change asked by another persona, none skipped; `stance` is `agree`, `concede` or `contest`; `alternative` is required on `contest` and may be `null` elsewhere; `withdrawn` may be an empty list; `revised_score` is an integer from 0 to 10.
