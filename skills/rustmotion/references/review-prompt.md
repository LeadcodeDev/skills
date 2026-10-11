# Pass 1 — review prompt

Fill the placeholders; the result is the briefing of a persona agent. Paths are absolute. The persona itself is not in the briefing: it is the agent's own prompt (see SKILL.md, Step 5).

---

Pass 1: your own review, written without seeing anyone else's.

Read the contact sheet `{sheet_path}`, the brief `{brief_path}` and the storyboard `{storyboard_path}`.

Score each criterion of your persona out of 10, then the video overall. Note what works and must be kept. Then list the changes you need, each tied to the timestamp of the still where the problem is visible. A `must` is something that makes you stop watching or stop believing. Everything else is a `should`.

Write your review to `{output_path}` in exactly this format: raw YAML, no code fence, nothing before or after:

```yaml
persona: {persona_id}
score: 7
criteria:
  - name: <criterion name as written in your persona file>
    score: 6
    why: "<one sentence>"
keeps:
  - at: "2.4s"
    what: "<what works, one sentence>"
changes:
  - at: "4.5s"
    issue: "<what is wrong, as you experience it>"
    fix: "<what you would need instead>"
    priority: must
```

Rules for the format: every free-text value is a double-quoted string, with any inner `"` escaped as `\"`, because an unquoted text containing `: ` breaks the YAML; `score` values are integers from 0 to 10; `at` is a timestamp printed on the sheet, quoted, with its `s`; `priority` is `must` or `should`; every criterion of your persona appears exactly once.
