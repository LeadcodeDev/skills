---
name: writing-article
description: Write complete, factually rigorous, sourced long-form articles for an Explainer blog (apps/blog/src/content/posts/). Use this whenever the user wants a blog article, a technical deep-dive, an explainer piece, a post-mortem, a benchmark write-up, or a write-up of something they built or investigated — including casual asks like "write a post about X", "draft an article on Y", "turn my notes into a blog post", "we should write this up". It runs the whole pipeline: inventorying and verifying sources, filling research gaps, checking coverage, drafting, humanizing the prose, and emitting publication-ready MDX. Prefer it over general drafting help whenever the output will be published and read by strangers, because it enforces sourcing and factual discipline that ordinary writing assistance does not. For Explainer documentation pages rather than articles, use explainer-content-writer instead.
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

**Never invent a specific.** Numbers, dates, version numbers, benchmark results, quotes, names, release timelines. These are exactly what a reader checks, and exactly where fabrication is unrecoverable. If a specific is not in a source, either find it or write around it: "several minutes" beats a made-up "4m12s", and being vague on purpose is honest in a way that being precise by accident is not.

**Quotation marks are a promise of verbatim.** A paraphrase inside quotes is a fabricated quote even when its substance is correct, because the reader will search for that string and not find it. Either copy the source exactly, or drop the marks and write "en substance". This is the easiest rule to break by accident: you read the source, you understand it, you write what it meant, and the quotes go on out of habit.

**Attribution is a claim like any other.** Before writing "selon X", open X and confirm the figure is there. A number that exists, attached to a source that exists, saying something that source never said, is the hardest error for a reader to catch and the most damaging when they do — and it survives every check except opening the link.

**When something cannot be verified, say so in the article.** Not in a hedge, not by softening the sentence into mush — explicitly, in the reliability callout (below). Burying uncertainty in careful phrasing is how a reader gets misled by an article that is technically never wrong.

---

## Evidence earns its place

Sourcing discipline says what a claim needs. It says nothing about how much evidence an article should carry, and left alone it drifts toward a literature review: every sentence propped up, every figure quoted, the author invisible behind the citations.

**Your own work is the spine, not an exhibit.** The article nobody else can write is the one about what you built, measured, broke, or renamed. External sources are there to hold up the parts your experience cannot reach — the history of a term, a study that contradicts the folklore, a number you did not measure yourself. When the piece opens with what you did and returns to it, the citations become support. When it opens with citations, your experience becomes an anecdote at the end.

**A number earns its place only if removing it changes the argument.** Three decimal figures in one paragraph do not make a point three times stronger; they make the reader skim. Keep the one that carries the claim, describe the rest in words: *à peu près deux fois plus de temps* is easier to trust and remember than *124 %*, and it is no less true.

**One source read properly beats five gestured at.** A methodology you actually took apart is worth more than a row of links, and it is the thing a reader could not have found alone.

**Cut the tour of the literature.** If four studies say the same thing, cite the best one. If a source only corroborates something already established, it is decoration.

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

If the subject genuinely has no serious counter-argument, that is worth a sentence explaining why. It is unusual, and asserting it without explanation reads as not having looked.

**These are things to cover, not headings to write.** A counter-argument belongs where the reader's objection forms, the limits belong beside the claim they bound, and what remains open belongs in the sentence that overreaches without it. The moment this list becomes a table of contents, it has stopped improving articles and started producing the same article every time.

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

### 7. Humanize the prose

Run the `humanizer` skill on the verified draft. This is not optional polish: a long-form draft carries LLM tells — hollow transitions, inflated vocabulary, reflexive hedging, rule-of-three padding, em-dash overuse — and a reader who notices them discounts the whole article, including the parts you sourced carefully. The factual work above is what earns trust; robotic prose is what stops it being spent.

Do this on the prose, before MDX formatting. The humanizer works on sentences, not directives, and running it first keeps the reliability callout out of its path entirely.

**Two of its patterns interact with the factual rules, in opposite directions.**

Pattern 5, *vague attributions and weasel words*, pulls the same way as this skill: it replaces "experts believe" with a named source. Let it work.

Pattern 20, *knowledge-cutoff disclaimers*, collides. It targets phrasing like "as of [date]" and "while specific details are limited" — which is the exact shape of an honest verification record. The two are not the same thing:

- A **model artifact** is the assistant apologising for its own training limits. Remove it.
- A **verification record** is the author stating what they tested, against which version, on what date, and what they could not confirm. Keep it, word for word.

If a sentence names what *you* checked, it is evidence. If it hedges about what *the model* knows, it is noise.

**Nothing factual may change in this pass.** Quotes, figures, dates, version numbers, benchmark results, link targets and the reliability callout are off limits — the humanizer rewrites voice, not evidence. Say so when invoking it.

Then re-check every specific against step 6. A rewriting pass over a sourced article can silently round a number or drop a qualifier, and that defect is invisible precisely because the prose reads better afterwards.

### 8. Format, then sweep

Produce the MDX (format below). Formatting is a rewriting pass like any other, and it undoes humanizer work: bolded paragraph openers creep back, em dashes reappear at the joins, headings drift into Title Case. This is observed behaviour, not a theoretical risk — it happens on almost every article.

So sweep the formatted file mechanically before showing it. Grep, do not eyeball:

- `—` outside quoted material. In French prose it is a legitimate mark, but it is also the most recognisable LLM tell there is, and the reader who spots the pattern discounts everything else. Recast with a comma, a colon, or parentheses. Inside a quotation, leave it: reproducing a source is not a style decision.
- bolded openings of paragraphs that are not genuine labels
- Title Case in headings, emoji, curly quotation marks
- every specific, re-checked against step 6

Then:

1. Show the draft in the conversation before writing any file.
2. Ask before saving, and if the file exists, read it and show what changes.

### French-specific rules

The article is usually in French, and these are register choices a reader notices even when they could not name them:

- **`que l'on`, never `qu'on`.** The elision reads as spoken French and cheapens written prose. It costs one word.
- Straight apostrophes and quotes, matching what the blog already publishes. Check an existing post rather than assuming.
- French quotation marks `« »` with non-breaking spaces inside, for quoted material.
- Keep technical vocabulary in English where that is what practitioners say (`commit`, `refactoring`, `build`). Inventing a French equivalent for a term the reader knows in English is worse than the English.

---

## Rigour is infrastructure, not display

The verification work above is what makes an article worth reading. Showing that work is what makes it tedious.

An article that opens with a methodology box, footnotes its own sourcing, and closes with a section confessing what the author could not establish reads as a thesis defence. The reader did not ask for a defence; they came for what you know. Apparatus signals anxiety, and anxiety is not authority — the writer who obviously knows the subject mentions evidence where it decides something and stays quiet elsewhere.

So: do all of it, show almost none of it.

**Disclosure belongs in the argument, at the point it bites.** When a claim rests on thin evidence, say so in the sentence that makes the claim, in your own voice, and move on:

> Je n'ai trouvé aucune étude qui mesure ça sur la durée. Ce qui suit repose sur des mesures transversales, et sur ce que j'ai vu dans mes propres dépôts.

That single clause does everything a disclosure box does, and it costs the reader nothing because it arrives exactly where the doubt would have formed. A box at the top asks them to hold a caveat in mind for two thousand words before it becomes relevant.

**Never build a section named after a rule you are following.** No *What I verified*, no *What I don't know*, no *Methodology*, no *Sources*. The coverage checklist is a list of things the article must **cover**, not a list of headings it must **contain** — a checklist that becomes a table of contents has stopped being a quality mechanism and started being a form.

**Version and date the claims that rot, not the article.** A stale claim is a specific claim, so pin it where it sits: `sur cargo 1.96` inside the sentence beats a banner declaring what the whole article was tested against.

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
