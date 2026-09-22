# Platform: Explainer blog

Long-form articles published to an [Explainer](https://github.com/LeadcodeDev/explainer) blog, under `apps/blog/src/content/posts/`. Read this once the platform is settled, before gathering sources.

The core skill decides what a claim owes the reader. This file decides what an *article* owes them, which is more: a reader who arrives from a search engine has no idea who you are, cannot ask you a question, and will check exactly one link before deciding whether to trust the rest.

**Documentation pages are a different job.** If the request is reference material for `apps/docs/` — API surface, configuration, a guide tied to a version — use `explainer-content-writer` instead. This platform is for articles that argue something.

---

## Rank what you find

Not all sourcing is equal, and an article that cites a blog post about a specification when the specification was one click away has told the reader something about its own care.

1. **Primary** — the specification, the RFC, the source code, the official documentation, the paper itself, the maintainer's own commit or issue comment
2. **Secondary** — a maintainer's blog post, a conference talk, a well-regarded technical write-up
3. **Tertiary** — aggregator articles, forum answers, anything summarizing a primary source you could read yourself

A claim about how software behaves should cite the software or its documentation, not an article about it. Tertiary sources are for finding primary ones, not for citing.

**If a claim survives only on tertiary sources, treat it as unverified.** Say so rather than laundering it through a citation that looks authoritative.

When the unsupported area is broad, or the topic is contested and needs claims checked against several independent sources, use the `deep-research` skill — it fans out searches and verifies claims adversarially, which is the expensive part done properly. For a small number of specific gaps, research them directly; dispatching a research harness at two missing figures costs more than it returns.

---

## Coverage, before drafting

Structure is free — pick whatever the subject wants. But an article does not go out missing any of these, because each one is a specific way articles fail readers:

- **Prerequisites** — what the reader must already know. Unstated, the article silently excludes people who would have benefited.
- **At least one serious counter-argument** — the strongest case against the claim, stated in a form its holders would recognize. An article that only argues one side reads as marketing, and readers discount it accordingly.
- **The limits** — where what you advance stops applying. Every technique has a domain; omitting it is how readers apply advice in situations it was never meant for.
- **What remains open** — what you do not know, what is contested, what would change your mind. This is what separates an article from a pitch.
- **An actionable next step** — what the reader does now. Docs, repository, a related article, a command to run.

If the subject genuinely has no serious counter-argument, that is worth a sentence explaining why. It is unusual, and asserting it without explanation reads as not having looked.

**These are things to cover, not headings to write.** A counter-argument belongs where the reader's objection forms, the limits belong beside the claim they bound, and what remains open belongs in the sentence that overreaches without it. The moment this list becomes a table of contents, it has stopped improving articles and started producing the same article every time.

---

## Link on the claim, not at the end

Link sources **inline, on the claim they support**, not in a bundle at the end. A reader checking one claim should not have to guess which of eleven references covers it. Inline links also make an unsourced claim visible while drafting, which is exactly when it is cheap to fix.

Open with the problem or the finding. No preamble about how important the topic is — the reader chose to open the article, that argument is already won.

---

## The mechanical sweep

Formatting is a rewriting pass like any other, and it undoes humanizer work: bolded paragraph openers creep back, em dashes reappear at the joins, headings drift into Title Case. This is observed behaviour, not a theoretical risk — it happens on almost every article.

So sweep the formatted file mechanically before showing it. Grep, do not eyeball:

- `—` outside quoted material. In French prose it is a legitimate mark, but it is also the most recognisable LLM tell there is, and the reader who spots the pattern discounts everything else. Recast with a comma, a colon, or parentheses. Inside a quotation, leave it: reproducing a source is not a style decision.
- bolded openings of paragraphs that are not genuine labels
- Title Case in headings, emoji, curly quotation marks
- every specific, re-checked against the verification pass

Where no shell is available, read the file top to bottom hunting for one defect at a time rather than all four at once. A single-purpose pass catches what a general re-read does not.

---

## French register, written

The article is usually in French, and these are register choices a reader notices even when they could not name them:

- **`que l'on`, never `qu'on`.** The elision reads as spoken French and cheapens written prose. It costs one word. (LinkedIn goes the other way — see `linkedin.md`. The rule is not about French, it is about register.)
- Straight apostrophes and quotes, matching what the blog already publishes. Check an existing post rather than assuming.
- French quotation marks `« »` with non-breaking spaces inside, for quoted material.
- Keep technical vocabulary in English where that is what practitioners say (`commit`, `refactoring`, `build`). Inventing a French equivalent for a term the reader knows in English is worse than the English.

---

## File format

The slug is kebab-case, derived from the title:

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

## Which component, and why

Reaching for a component is an editorial decision; writing it correctly is a syntax question, and the syntax lives in `explainer-mdx.md` alone. Choose here, then go there for the how.

- **Callout** — a caveat or a breaking change the reader must not miss at that point in the argument. Not for disclosure of thin evidence: that belongs in the sentence, in your voice.
- **Card Group + Card** — a collection of links that are genuinely alternatives to each other. Two cards is usually a list in disguise.
- **Steps** — something the reader performs in order, where doing step three before step two fails.
- **Code Group** — the same thing expressed several ways, side by side. Package-manager variants live here.
- **Tabs** — only when a tab holds prose as well as code. If every tab is a bare code block, Code Group does the same job as a directive, with no client-side hydration to pay for.

Default to prose. Every component is a decision the reader has to parse before they can read, and an article built from containers reads as documentation.

**All MDX components are auto-imported** — never write an import statement.

Nesting uses increasing colon counts, and getting it wrong is the single most common MDX failure. The rule, the counts per component, and the same-level collision gotcha are in `explainer-mdx.md`; read it before writing any component you are not certain of, along with the code-block features (highlighting, diffs, file labels).
