---
name: writing
description: Use when the user wants something written for publication under their own name — a blog article, technical deep-dive, explainer, post-mortem, benchmark write-up, or a LinkedIn post — including casual asks like "write a post about X", "draft an article on Y", "turn my notes into a blog post", "j'ai fait X ça mériterait un post", "we should write this up". Also use when a draft already exists and needs to be made publishable, or when notes, a transcript, or a changelog are to become something a stranger reads. Two platforms are supported: an Explainer blog under apps/blog/src/content/posts/, and LinkedIn. For Explainer documentation pages rather than articles, use explainer-content-writer instead.
---

# Writing for publication

Text that a stranger can trust. The discipline that gets it there is factual, not stylistic.

The premise: what you publish is read by people who cannot check your work. They extend credit. A single invented number, misattributed quote, or confidently-stated guess spends that credit permanently — and they will never tell you, they will just stop reading. Everything below exists to protect that.

---

## Step 0 — settle the platform

**Do this before gathering anything.** The platform is not a formatting decision taken at the end; it changes what counts as evidence, how much research the piece needs, what register the prose is in, and what the output even is.

| Platform | Read | The piece is |
|----------|------|--------------|
| **Explainer blog** | `references/explainer.md` | A long-form article with inline sources, published as MDX |
| **LinkedIn** | `references/linkedin.md` | A first-person account, plain text, no sources to link |

If the request does not say, ask. It is one question and it costs nothing; discovering at the formatting stage that you wrote the wrong shape costs the whole draft.

Read the platform file once, at this step. Everything in this document applies to both.

---

## The claim discipline

Every sentence is one of three things. Confusing them is the most common way otherwise good writing becomes untrustworthy.

| Kind | What it needs | How it reads |
|------|---------------|--------------|
| **Fact** | A source | "Cargo takes a lock on the target directory" |
| **Inference** | The facts it rests on, stated | "Which means two agents building concurrently will serialize" |
| **Opinion** | First person, owned | "I think the worktree is the wrong trade here" |

Write them so the reader can tell which is which without effort. An opinion in the grammar of a fact ("worktrees are the wrong trade") is the failure mode — it borrows the authority of evidence without carrying any.

**Never invent a specific.** Numbers, dates, version numbers, benchmark results, quotes, names, release timelines. These are exactly what a reader checks, and exactly where fabrication is unrecoverable. If a specific is not in a source, either find it or write around it: "several minutes" beats a made-up "4m12s", and being vague on purpose is honest in a way that being precise by accident is not.

**Quotation marks are a promise of verbatim.** A paraphrase inside quotes is a fabricated quote even when its substance is correct, because the reader will search for that string and not find it. Either copy the source exactly, or drop the marks and write "en substance". This is the easiest rule to break by accident: you read the source, you understand it, you write what it meant, and the quotes go on out of habit.

**Attribution is a claim like any other.** Before writing "selon X", open X and confirm the figure is there. A number that exists, attached to a source that exists, saying something that source never said, is the hardest error for a reader to catch and the most damaging when they do — and it survives every check except opening the link.

**When something cannot be verified, say so in the piece.** Not in a hedge, not by softening the sentence into mush — explicitly, in your own voice, where the claim sits. Burying uncertainty in careful phrasing is how a reader gets misled by something that is technically never wrong.

Where the reader has no link to follow, these rules tighten rather than relax. See the platform file.

---

## Evidence earns its place

Sourcing discipline says what a claim needs. It says nothing about how much evidence a piece should carry, and left alone it drifts toward a literature review: every sentence propped up, every figure quoted, the author invisible behind the citations.

**Your own work is the spine, not an exhibit.** The piece nobody else can write is the one about what you built, measured, broke, or renamed. External sources are there to hold up the parts your experience cannot reach — the history of a term, a study that contradicts the folklore, a number you did not measure yourself. When it opens with what you did and returns to it, the citations become support. When it opens with citations, your experience becomes an anecdote at the end.

**A number earns its place only if removing it changes the argument.** Three decimal figures in one paragraph do not make a point three times stronger; they make the reader skim. Keep the one that carries the claim, describe the rest in words: *à peu près deux fois plus de temps* is easier to trust and remember than *124 %*, and it is no less true.

**One source read properly beats five gestured at.** A methodology you actually took apart is worth more than a row of links, and it is the thing a reader could not have found alone.

**Cut the tour of the literature.** If four studies say the same thing, cite the best one. If a source only corroborates something already established, it is decoration.

---

## Workflow

### 1. Establish what it argues

Before gathering anything, get two things straight:

- **The claim** — what the reader should believe or be able to do afterwards, in one sentence. Without one, the piece becomes a list of facts, which is a reference page.
- **The reader** — what they already know. This sets the prerequisite floor and is the difference between condescending and incomprehensible.

Ask if either is unclear. This is the cheapest possible moment to ask; every later question costs a redraft.

### 2. Inventory the sources you have

Take what the user supplied — links, specs, source files, their own notes, transcripts of what they did. Their own work is a primary source and usually the best one available: they were there.

Then list, explicitly, which parts of the intended claim are **not** yet supported. That list is the research plan.

**Read the notes for judgements as well as facts.** Raw notes carry verdicts about tools and people that were written for an audience of one. What happens to those depends on the platform, and it is never "relay them unchanged".

### 3. Fill the gaps

How much research this deserves is a platform question — an article with a bibliography and a first-person post have very different appetites. The platform file says how far to go and which sources count.

### 4. Check coverage

Also platform-specific, and the lists genuinely differ. Read the platform file before drafting rather than writing to the wrong one and cutting later.

### 5. Draft

Open with the problem or the finding. No preamble about how important the topic is — the reader chose to open this, that argument is already won.

Keep the voice direct and human. Prefer the concrete example over the general statement, the short sentence over the qualified one.

### 6. Verify before formatting

Re-read the draft hunting only for factual defects, with its own claims treated as suspect:

- every specific — traced to a source, or removed
- every link — actually points at what the sentence says it does
- every inference — the facts under it are present in the text, not just in your head
- every claim you would be embarrassed to be wrong about — checked again
- every judgement about someone else's work — is it yours to publish?

This pass is cheap now and impossible later. A correction reaches a fraction of the people the error did.

### 7. Humanize the prose

Run the `humanizer` skill on the verified draft. This is not optional polish: a draft carries LLM tells — hollow transitions, inflated vocabulary, reflexive hedging, rule-of-three padding, em-dash overuse — and a reader who notices them discounts everything, including the parts you sourced carefully. The factual work is what earns trust; robotic prose is what stops it being spent.

Do this on the prose, before any platform formatting.

**Three of its patterns interact with the rules here.**

Pattern 5, *vague attributions and weasel words*, pulls the same way: it replaces "experts believe" with a named source. Let it work.

Pattern 20, *knowledge-cutoff disclaimers*, collides. It targets phrasing like "as of [date]" and "while specific details are limited" — the exact shape of an honest verification record. The two are not the same thing:

- A **model artifact** is the assistant apologising for its own training limits. Remove it.
- A **verification record** is the author stating what they tested, against which version, on what date, and what they could not confirm. Keep it, word for word.

If a sentence names what *you* checked, it is evidence. If it hedges about what *the model* knows, it is noise.

Pattern 17, *emoji*, holds on the blog without exception. On LinkedIn it depends on an answer the user gives before drafting: if they accepted emoji bullets, the humanizer must not strip them. Check which answer was given before invoking it.

**Nothing factual may change in this pass.** Quotes, figures, dates, version numbers, benchmark results and link targets are off limits — the humanizer rewrites voice, not evidence. Say so when invoking it.

If the skill is unavailable, do the pass manually against the same tells rather than skipping it.

Then re-check every specific against step 6. A rewriting pass can silently round a number or drop a qualifier, and that defect is invisible precisely because the prose reads better afterwards.

### 8. Format, then sweep

Apply the platform format, then sweep the result mechanically — formatting is a rewriting pass like any other and it undoes humanizer work. The platform file says what to sweep for.

Then, always:

1. Show the draft in the conversation before writing any file.
2. Ask before saving, and if the file already exists, read it and show what changes.

---

## Rigour is infrastructure, not display

The verification work above is what makes a piece worth reading. Showing that work is what makes it tedious.

Something that opens with a methodology box, footnotes its own sourcing, and closes with a section confessing what the author could not establish reads as a thesis defence. The reader did not ask for a defence; they came for what you know. Apparatus signals anxiety, and anxiety is not authority — the writer who obviously knows the subject mentions evidence where it decides something and stays quiet elsewhere.

So: do all of it, show almost none of it.

**Disclosure belongs in the argument, at the point it bites.** When a claim rests on thin evidence, say so in the sentence that makes the claim, in your own voice, and move on:

> Je n'ai trouvé aucune étude qui mesure ça sur la durée. Ce qui suit repose sur des mesures transversales, et sur ce que j'ai vu dans mes propres dépôts.

That single clause does everything a disclosure box does, and it costs the reader nothing because it arrives exactly where the doubt would have formed. A box at the top asks them to hold a caveat in mind for two thousand words before it becomes relevant.

**Never build a section named after a rule you are following.** No *What I verified*, no *What I don't know*, no *Methodology*, no *Sources*. A coverage checklist is a list of things to **cover**, not a list of headings to **contain** — a checklist that becomes a table of contents has stopped being a quality mechanism and started being a form.

**Version and date the claims that rot, not the piece.** A stale claim is a specific claim, so pin it where it sits: `sur cargo 1.96` inside the sentence beats a banner declaring what the whole thing was tested against.

---

## Reference files

Read the one for the platform, at step 0, once per piece.

- `references/explainer.md` — Explainer blog: source ranking, coverage, inline linking, written French register, file path and frontmatter, which component to reach for, and the full MDX syntax for writing it. The syntax half is a lookup at drafting time, not part of the planning read.
- `references/linkedin.md` — LinkedIn: the arc, the one permitted target of criticism, form and output contract
