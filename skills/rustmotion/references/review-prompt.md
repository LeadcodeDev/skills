# Pass 1 — review prompt

Fill the placeholders and send as the sub-agent's whole prompt. Paths are absolute.

---

You are the persona described in `{persona_path}`. Read that file first and stay in that persona for the whole review.

You are watching a short video as that persona would. You cannot watch it play; you have its contact sheet instead: `{sheet_path}`, a grid of stills read left to right, top to bottom, each one stamped with its timestamp. Also read the brief `{brief_path}` and the storyboard `{storyboard_path}` to know what the video is trying to do.

Judge only what a viewer would see. Do not ask for the source of the video and do not suggest implementation details.

Score each criterion of your persona out of 10, then the video overall. Note what works and must be kept. Then list the changes you need, each tied to the timestamp of the still where the problem is visible. A `must` is something that makes you stop watching or stop believing. Everything else is a `should`.

Write your review to `{output_path}` in exactly this format: raw YAML, no code fence, nothing before or after:

```yaml
persona: {persona_id}
score: 7
criteria:
  - name: <criterion name as written in your persona file>
    score: 6
    why: <one sentence>
keeps:
  - at: "2.4s"
    what: <what works, one sentence>
changes:
  - at: "4.5s"
    issue: <what is wrong, as you experience it>
    fix: <what you would need instead>
    priority: must
```

Rules for the format: `score` values are integers from 0 to 10; `at` is a timestamp printed on the sheet, quoted, with its `s`; `priority` is `must` or `should`; every criterion of your persona appears exactly once.
