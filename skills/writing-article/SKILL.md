---
name: writing-article
description: Write complete, factually rigorous, sourced long-form articles for an Explainer blog (apps/blog/src/content/posts/). Use this whenever the user wants a blog article, a technical deep-dive, an explainer piece, a post-mortem, a benchmark write-up, or a write-up of something they built or investigated — including casual asks like "write a post about X", "draft an article on Y", "turn my notes into a blog post", "we should write this up". It runs the whole pipeline: inventorying and verifying sources, filling research gaps, checking coverage, drafting, and emitting publication-ready MDX. Prefer it over general drafting help whenever the output will be published and read by strangers, because it enforces sourcing and factual discipline that ordinary writing assistance does not. For Explainer documentation pages rather than articles, use explainer-content-writer instead.
---

# Explainer Article Writer

Long-form articles that a stranger can trust. The output is publication-ready MDX in an Explainer blog; the discipline that gets it there is factual, not stylistic.

The premise: an article is read by people who cannot check your work. They extend credit. A single invented number, misattributed quote, or confidently-stated guess spends that credit permanently — and they will never tell you, they will just stop reading. Everything below exists to protect that.

**Documentation pages are a different job.** If the request is reference material for `apps/docs/` — API surface, configuration, a guide tied to a version — use `explainer-content-writer`. This skill is for articles that argue something.

---

## The claim discipline

Every sentence in a technical article is one of three things. Confusing them is the most common way an otherwise good article becomes untrustworthy.

| Kind | What it needs | How it reads |
|------|---------------|--------------|
| **Fact** | A source, linked at the claim | "Cargo takes a lock on the target directory" + link |
| **Inference** | The facts it rests on, stated | "Which means two agents building concurrently will serialize" |
| **Opinion** | First person, owned | "I think the worktree is the wrong trade here" |

Write them so the reader can tell which is which without effort. An opinion in the grammar of a fact ("worktrees are the wrong trade") is the failure mode — it borrows the authority of evidence without carrying any.

**Never invent a specific.** Numbers, dates, version numbers, benchmark results, quotes, names, release timelines. These are exactly what a reader checks, and exactly where fabrication is unrecoverable. If a specific is not in a source, either find it or write around it — "several minutes" beats a made-up "4m12s", and being vague on purpose is honest in a way that being precise by accident is not.

**When something cannot be verified, say so in the article.** Not in a hedge, not by softening the sentence into mush — explicitly, in the reliability callout (below). Burying uncertainty in careful phrasing is how a reader gets misled by an article that is technically never wrong.

---

## Workflow

### 1. Establish what the article argues

Before gathering anything, get two things straight:

- **The claim** — what the reader should believe or be able to do afterwards, in one sentence. An article without a claim becomes a list of facts, which is a reference page, not an article.
- **The reader** — what they already know. This sets the prerequisite floor and is the difference between condescending and incomprehensible.

Ask if either is unclear. This is the cheapest possible moment to ask; every later question costs a redraft.

### 2. Inventory the sources you have

Take what the user supplied — links, specs, source files, their own notes, transcripts of what they did. Their own work is a primary source and usually the best one in the article: they were there.

Then list, explicitly, which parts of the intended claim are **not** yet supported. That list is the research plan.

### 3. Fill the gaps

For a small number of specific gaps, research them directly. When the unsupported area is broad, or the topic is contested and needs claims checked against several independent sources, use the `deep-research` skill — it fans out searches and verifies claims adversarially, which is the expensive part done properly.

Rank what you find, because not all sourcing is equal:

1. **Primary** — the specification, the RFC, the source code, the official documentation, the paper itself, the maintainer's own commit or issue comment
2. **Secondary** — a maintainer's blog post, a conference talk, a well-regarded technical write-up
3. **Tertiary** — aggregator articles, forum answers, anything summarizing a primary source you could read yourself

A claim about how software behaves should cite the software or its documentation, not an article about it. Tertiary sources are for finding primary ones, not for citing.

**If a claim survives only on tertiary sources, treat it as unverified.** Say so rather than laundering it through a citation that looks authoritative.

### 4. Check coverage before drafting

Structure is free — pick whatever the subject wants. But an article does not go out missing any of these, because each one is a specific way articles fail readers:

- **Prerequisites** — what the reader must already know. Unstated, the article silently excludes people who would have benefited.
- **At least one serious counter-argument** — the strongest case against the claim, stated in a form its holders would recognize. An article that only argues one side reads as marketing, and readers discount it accordingly.
- **The limits** — where what you advance stops applying. Every technique has a domain; omitting it is how readers apply advice in situations it was never meant for.
- **What remains open** — what you do not know, what is contested, what would change your mind. This is what separates an article from a pitch.
- **An actionable next step** — what the reader does now. Docs, repository, a related article, a command to run.

If the subject genuinely has no serious counter-argument, that is worth a sentence explaining why — it is unusual, and asserting it without explanation reads as not having looked.

### 5. Draft

Open with the problem or the finding. No preamble about how important the topic is — the reader chose to open the article, that argument is already won.

Link sources **inline, on the claim they support**, not in a bundle at the end. A reader checking one claim should not have to guess which of eleven references covers it. Inline links also make an unsourced claim visible while drafting, which is exactly when it is cheap to fix.

Keep the voice direct and human. Prefer the concrete example over the general statement, the short sentence over the qualified one.

### 6. Verify before formatting

Re-read the draft hunting only for factual defects, with the article's own claims treated as suspect:

- every specific — traced to a source, or removed
- every link — actually points at what the sentence says it does
- every inference — the facts under it are present in the article, not just in your head
- every claim you would be embarrassed to be wrong about — checked again

This pass is cheap now and impossible later. A correction on a published article reaches a fraction of the people the error did.

### 7. Format, disclose, save

Produce the MDX (format below), add the reliability callout, then:

1. Show the draft in the conversation before writing any file.
2. Offer the `humanizer` skill — long-form drafts carry LLM tells (hollow transitions, inflated vocabulary, reflexive hedging) that undercut the credibility everything above was protecting.
3. Ask before saving, and if the file exists, read it and show what changes.

---

## The reliability callout

Every article carries one, near the top. It is the disclosure that makes the rest trustworthy: a reader who sees you name your own weak points extends credit to the parts you state plainly.

```mdx
:::callout{variant="info"}
Verified against Rust 1.84 and cargo 1.84.0 on 2026-08-05. The concurrency
figures come from a single machine and will differ on yours. I could not
confirm whether the target-directory lock behaves the same on Windows —
everything here was tested on macOS and Linux.
:::
```

Three things belong in it:

- **What was verified, against what version, when.** Technical articles rot. A dated, versioned claim ages honestly; an undated one silently becomes wrong.
- **What could not be verified.** Named, not hinted at.
- **Where the evidence is thin.** A single benchmark, one machine, a sample of one, a vendor's own numbers.

Use `variant="warning"` instead when a genuinely load-bearing claim is unverified — the reader should meet that before investing in the article, not after.

---

## Explainer blog format

File path — the slug is kebab-case, derived from the title:

```
apps/blog/src/content/posts/{locale}/{slug}.mdx
```

Locales are `en` and `fr`. Ask which, or both; if both, write the second as a natural article in that language rather than a translation of the first.

Frontmatter:

```yaml
---
title: "Article Title"              # required — h1 and post cards
description: "Full description."    # required — SEO meta and OG image subtitle
short_description: "Short hook."    # optional — post card on hover
date: 2026-08-05                    # required — ISO; today's date unless specified
tags: [tag1, tag2]                  # optional — lowercase, kebab-case
cover: /images/cover.jpg            # optional — hero image
status: draft                       # required — "draft" or "published"
author: author_id                   # optional — must exist in src/lib/authors.ts
---
```

New articles are written `status: draft` unless the user says otherwise. Publishing is theirs to decide.

**All MDX components are auto-imported** — never write an import statement.

The components an article actually uses:

| Component | Syntax | Use for |
|-----------|--------|---------|
| Callout | `:::callout{variant="info"}` | The reliability note, caveats, breaking changes |
| Card Group | `::::card-group{cols=2}` | Link collections, further reading |
| Card | `:::card{label="..." icon="lucide:..."}` | One card inside a group |
| Steps | `::::step-group` + `:::step{title="..."}` | Anything sequential |
| Code Group | `:::codegroup` + labeled blocks | Alternatives side by side |
| Tabs | `<Tabs items={[...]} client:load>` | Package manager or config variants |

Nesting uses **increasing colon counts** — the outer container always carries more colons than what it holds. Getting this wrong is the single most common MDX failure:

```
::::card-group    (4) — container
  :::card         (3) — leaf
  :::
::::
```

Full component syntax, code-block features (highlighting, diffs, labels), and the per-component gotchas: read `references/explainer-mdx.md` before writing a component you are not certain of.

---

## Reference files

- `references/explainer-mdx.md` — complete MDX component syntax and code-block features. Read it once per session, when writing components.
