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

Reaching for a component is an editorial decision; writing it correctly is a syntax question. Choose here, then drop to [Component and code-block syntax](#component-and-code-block-syntax) for the how.

- **Callout** — a caveat or a breaking change the reader must not miss at that point in the argument. Not for disclosure of thin evidence: that belongs in the sentence, in your voice.
- **Card Group + Card** — a collection of links that are genuinely alternatives to each other. Two cards is usually a list in disguise.
- **Steps** — something the reader performs in order, where doing step three before step two fails.
- **Code Group** — the same thing expressed several ways, side by side. Package-manager variants live here.
- **Tabs** — only when a tab holds prose as well as code. If every tab is a bare code block, Code Group does the same job as a directive, with no client-side hydration to pay for.

Default to prose. Every component is a decision the reader has to parse before they can read, and an article built from containers reads as documentation.

**All MDX components are auto-imported** — never write an import statement.

Nesting uses increasing colon counts, and getting it wrong is the single most common MDX failure. The rule, the counts per component, and the same-level collision gotcha are all below.

---

## Component and code-block syntax

Everything below is the how, for the components chosen above. It is deliberately the blog subset: documentation-only concerns are out of scope.

**This section is a lookup, not a reading.** Jump to the component you are about to write and skip the rest. Nothing here is needed to decide what the article argues, what it must cover, or how its sources rank, which is why it sits after all of that rather than before.

### Contents

- [Nesting and colon counts](#nesting-and-colon-counts)
- [Callout](#callout)
- [Card Group + Card](#card-group--card)
- [Steps](#steps)
- [Code Group](#code-group)
- [Tabs](#tabs)
- [Code block features](#code-block-features)
  - [File label](#file-label)
  - [Line highlighting](#line-highlighting)
  - [Word highlighting](#word-highlighting)
  - [Diffs](#diffs)
  - [Focus mode](#focus-mode)
  - [Error highlighting](#error-highlighting)
  - [Combining annotations](#combining-annotations)
  - [Language icons](#language-icons)
- [Cheat sheets](#cheat-sheets)

---

### Nesting and colon counts

Directive components are delimited by colons. The outer wrapper always has more colons than what is inside it — each nesting level adds one colon.

```
::::card-group     ← 4 (container)
  :::card          ← 3 (leaf)
  :::
::::
```

Directives require at least 3 colons. Two colons is not recognized as a directive at all.

Two containers in this subset take 4 colons: `card-group` and `step-group`. Their children (`card`, `step`) take 3. `callout` and `codegroup` take 3 and hold no directive children.

**Gotcha — same-level collision.** Two directives at the same colon count cannot be distinguished by the parser when one is nested inside the other and left unclosed. Always close every directive before opening the next one at the same level. See [Steps](#steps) for the concrete failure.

---

### Callout

Colored boxes with icons for highlighting important content. Content supports full Markdown.

#### Variants

| Variant | Color | Icon | Use for |
|---------|-------|------|---------|
| `info` | Blue | ℹ️ | General tips, notes (default) |
| `success` | Green | ✓ | Positive outcomes, confirmations |
| `warning` | Yellow | ⚠️ | Cautions, deprecations |
| `danger` | Red | ✕ | Critical warnings, breaking changes |

#### Directive syntax (preferred)

Three colons. Always state the variant explicitly.

```mdx
:::callout{variant="info"}
This is a general note, with **Markdown** support.
:::

:::callout{variant="success"}
The installation completed successfully.
:::

:::callout{variant="warning"}
This option is deprecated in v2.
:::

:::callout{variant="danger"}
This action is irreversible.
:::
```

#### JSX alternative — required for a title

The `title` attribute works only in JSX syntax.

```mdx
<Callout variant="info" title="Prerequisites">
  Make sure you have Node.js 22+ installed.
</Callout>
```

#### Props

| Prop | Type | Default | Note |
|------|------|---------|------|
| `variant` | `"info"` \| `"success"` \| `"warning"` \| `"danger"` | `"info"` | State it explicitly |
| `title` | `string` | — | JSX syntax only |

#### Gotchas

- **`title` in directive syntax is silently ignored.** `:::callout{variant="info" title="My Title"}` renders without the title and gives no error. Use `<Callout variant="info" title="...">` when you need one.
- **Missing closing `:::`.** The directive never closes; everything after it is swallowed into the callout or breaks the page.
- **Wrong colon count.** `::callout{...}` with two colons is not recognized. Directives require 3+ colons.
- **Invalid variant value.** `variant="note"` is not valid. Only `info`, `success`, `warning`, `danger`.
- **Faking a title with bold text.** `**Warning:** ...` as the first line works visually but is just bold content, not a semantic title. Use the JSX `title` prop instead.
- **Import statements.** Never write `import { Callout } from '@explainer/mdx'`. All components are auto-imported.

---

### Card Group + Card

Responsive grid for related content, links, or further reading. `::::card-group` (4 colons) contains `:::card` (3 colons). Card content supports full Markdown, and `href` makes the entire card clickable.

#### Syntax

Two columns (default):

```mdx
::::card-group{cols=2}
  :::card{label="Getting Started" icon="lucide:rocket" href="/en/explainer/getting-started"}
  Set up Explainer in under 5 minutes.
  :::

  :::card{label="Configuration" icon="lucide:settings"}
  Customize every aspect of your docs.
  :::
::::
```

Three columns:

```mdx
::::card-group{cols=3}
  :::card{label="Callout" icon="lucide:message-circle"}
  Highlight important information.
  :::

  :::card{label="Steps" icon="lucide:list-ordered"}
  Sequential numbered instructions.
  :::

  :::card{label="Tabs" icon="lucide:columns"}
  Switchable tab panels.
  :::
::::
```

The icon is optional:

```mdx
::::card-group{cols=2}
  :::card{label="Docs"}
  Technical documentation.
  :::

  :::card{label="Blog"}
  Articles and tutorials.
  :::
::::
```

#### CardGroup props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `cols` | `1` \| `2` \| `3` \| `4` | `2` | Max columns in the grid |

#### Card props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `label` | `string` | Yes | Card title |
| `icon` | `string` | No | Iconify identifier, e.g. `lucide:rocket` |
| `href` | `string` | No | Makes the card a clickable link |

**Finding icons**: browse [icon-sets.iconify.design](https://icon-sets.iconify.design). Common sets: `lucide:`, `devicon:`, `mdi:`.

#### Gotchas

- **`card-group` with 3 colons.** Both directives end up at the same nesting level and the parser does not recognize the group. The container needs 4 (`::::`).
- **`card` with 4 colons.** It competes with the parent container and breaks nesting. Cards need exactly 3 (`:::`).
- **Missing `label`.** It is required; without it the card does not render correctly.
- **`cols` as a quoted string.** Write `{cols=2}`, not `{cols="2"}` — it takes a bare number.
- **A standalone `:::card`.** Cards must always be nested inside a `::::card-group`. A card outside a group is not a supported pattern.
- **Icon without a set prefix.** `icon="rocket"` does not resolve. Use `lucide:rocket`, `devicon:typescript`, `mdi:home`.

---

### Steps

Sequential instructions with automatic numbering and a vertical progress indicator. `::::step-group` (4 colons) contains `:::step` (3 colons). Nesting other components — callouts, code blocks — inside a step is fully supported.

#### Syntax

````mdx
::::step-group
  :::step{title="Install dependencies"}
  Run the install command:

  ```bash
  pnpm install
  ```
  :::

  :::step{title="Configure environment"}
  Copy the example file:

  ```bash
  cp .env.example .env.local
  ```
  :::

  :::step{title="Start the dev server"}
  ```bash
  pnpm dev
  ```
  :::
::::
````

Rich content — paragraphs and links — works the same way:

````mdx
::::step-group
  :::step{title="Create a GitHub repository"}
  Go to [github.com/new](https://github.com/new) and create a new repository. You can leave it empty: Explainer will push the initial commit.
  :::

  :::step{title="Push your code"}
  ```bash
  git remote add origin https://github.com/you/your-repo
  git push -u origin main
  ```
  :::
::::
````

A step with an embedded callout — note that both close before the step closes:

````mdx
::::step-group
  :::step{title="Deploy to production"}
  Run the deploy command:
  ```bash
  pnpm deploy
  ```

  :::callout{variant="success"}
  Your app will be live within 30 seconds.
  :::
  :::
::::
````

#### Props

**step-group** — no props, acts as container.

| step prop | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | `string` | Yes | Displayed next to the step number |

#### Gotchas

- **`step` outside a `step-group`.** It renders without numbering or the visual indicator. A `:::step` must always be nested inside `::::step-group`.
- **`step-group` with 3 colons.** Both directives land at the same level and nesting breaks. The container needs 4 (`::::`).
- **Missing `title`.** It is required; without it the step label does not render.
- **Manual numbering in the title.** `title="1. Install dependencies"` produces "1. 1. Install dependencies" because numbers are auto-generated. Write titles without numbers.
- **Unclosed inner callout.** A callout and a step use the same colon count (`:::`), so if the callout is not closed the parser cannot tell where it ends and the next step begins. Broken — do not copy:

  ````mdx
  ::::step-group
    :::step{title="Step with callout"}
    :::callout{variant="info"}
    Important note.
    :::step{title="Next step"}
    Content.
    :::
  ::::
  ````

  Close every directive before opening the next one at the same level.

---

### Code Group

Multiple code blocks as switchable tabs. Lighter than `Tabs` for pure code comparison — directive-based, no JSX, no `client:load`. `:::codegroup` (3 colons) wraps standard fenced code blocks, and every block needs a `[label]` in its meta string to generate a tab. The label is also shown as the file label in the code block header.

#### Syntax

Multiple files, same language:

````mdx
:::codegroup
  ```ts [index.ts]
  import { createApp } from './app'
  const app = createApp()
  app.listen(3000)
  ```

  ```ts [app.ts]
  export function createApp() {
    return { listen(port: number) { console.log(`Port ${port}`) } }
  }
  ```
:::
````

Multi-language comparison:

````mdx
:::codegroup
  ```ts [TypeScript]
  const greet = (name: string) => `Hello, ${name}!`
  ```

  ```python [Python]
  def greet(name: str) -> str:
      return f"Hello, {name}!"
  ```

  ```go [Go]
  func greet(name string) string {
      return fmt.Sprintf("Hello, %s!", name)
  }
  ```
:::
````

Package managers — the directive alternative to `<Tabs>` for pure commands:

````mdx
:::codegroup
  ```bash [pnpm]
  pnpm add @explainer/ui
  ```

  ```bash [npm]
  npm install @explainer/ui
  ```

  ```bash [yarn]
  yarn add @explainer/ui
  ```
:::
````

#### Gotchas

- **Code blocks without `[label]`.** The tabs have no title and the group does not render properly. Every block inside a `:::codegroup` must carry a `[label]`.
- **Wrong directive name.** It is `:::codegroup`, one word, no hyphen. `:::code-group` is not recognized.
- **JSX children inside a code group.** `:::codegroup` expects raw fenced code blocks, not `<Tab>` elements. Mixing the two syntaxes does not work.
- **A single code block.** A group with one block is a plain code block with extra markup. Use a plain fenced code block instead.
- **Special characters in labels.** `[src/index.ts (main entry)]` may cause unexpected behavior. Keep labels short and simple: `[src/index.ts]`, `[TypeScript]`.

---

### Tabs

Switchable tab panels. `Tabs` and `Tab` are React components: JSX syntax only, and they require the `client:load` Astro directive.

#### Syntax

````mdx
<Tabs items={["pnpm", "npm", "yarn"]} client:load>
  <Tab label="pnpm">
    ```bash
    pnpm install @explainer/ui
    ```
  </Tab>
  <Tab label="npm">
    ```bash
    npm install @explainer/ui
    ```
  </Tab>
  <Tab label="yarn">
    ```bash
    yarn add @explainer/ui
    ```
  </Tab>
</Tabs>
````

Mixed content — text plus code — is where `Tabs` earns its extra weight over `:::codegroup`:

````mdx
<Tabs items={["Basic", "Advanced"]} client:load>
  <Tab label="Basic">
    A minimal configuration:
    ```ts [astro.config.ts]
    export default defineConfig({})
    ```
  </Tab>
  <Tab label="Advanced">
    Full configuration with all options:
    ```ts [astro.config.ts]
    export default defineConfig({
      integrations: [react(), mdx()],
      output: 'static',
    })
    ```
  </Tab>
</Tabs>
````

#### Props

**Tabs**

| Prop | Type | Description |
|------|------|-------------|
| `items` | `string[]` | Tab labels displayed in the header |
| `client:load` | — | Astro directive; always required |

**Tab**

| Prop | Type | Description |
|------|------|-------------|
| `label` | `string` | Must match an entry in `items` |

#### Tabs vs Code Group

If the content is purely code blocks with no surrounding text, prefer `:::codegroup` — it is simpler, directive-based, and needs no `client:load`. Use `<Tabs>` when tabs contain mixed content: text plus code, or multiple sections.

#### Gotchas

- **Missing `client:load`.** Astro does not hydrate the component. The tabs render as static HTML and clicking them switches nothing.
- **Label does not match `items`.** Labels are case-sensitive: `label="Npm"` does not match `"npm"`. The tab either fails to render or shows an incorrect active state.
- **Directive syntax.** There is no `:::tabs` equivalent. `Tabs` and `Tab` only work as JSX.
- **`items` as a string.** It must be a JavaScript array: `items={["pnpm", "npm", "yarn"]}`. A plain string does not parse into tabs.
- **Mismatched `<Tab>` count.** If `items` declares 3 tabs but only 2 `<Tab>` children exist, the third header renders and shows nothing when clicked. Match the count exactly.

---

### Code block features

Standard fenced code blocks with a language identifier get syntax highlighting automatically (Shiki, dual light/dark themes). Everything below is opt-in via inline annotations or meta string options.

#### File label

Add `[path/to/file]` in the meta string to display a label in the code block header.

````mdx
```ts [src/index.ts]
import { createApp } from './app'
const app = createApp()
```
````

Labels work with any language. The same label is used as the tab title inside a `:::codegroup`.

#### Line highlighting

Inline annotation — add `// [!code highlight]` at the end of a line:

````mdx
```ts
function setup() {
  const config = loadConfig() // [!code highlight]
  return config
}
```
````

Meta range syntax — use `{lines}` in the meta string:

````mdx
```ts {1,3-4}
import { defineConfig } from 'astro/config'
import react from '@astrojs/react'
import mdx from '@astrojs/mdx'
import tailwind from '@astrojs/tailwindcss'
```
````

| Syntax | Meaning |
|--------|---------|
| `{3}` | Line 3 only |
| `{1,4}` | Lines 1 and 4 |
| `{1-3}` | Lines 1 through 3 |
| `{1,3-5,8}` | Line 1, lines 3–5, and line 8 |

#### Word highlighting

Highlight every occurrence of a word. The comment itself is hidden from output:

````mdx
```ts
// [!code word:config]
const config = loadConfig()
validateConfig(config)
applyConfig(config)
```
````

#### Diffs

Mark lines as added or removed. The annotation comment is stripped from the output. Removed lines render red, added lines render green.

````mdx
```ts
function createUser(name: string) {
  const user = { name }                      // [!code --]
  const user = { name, id: generateId() }    // [!code ++]
  return user
}
```
````

#### Focus mode

Dim all lines except those annotated with `// [!code focus]`:

````mdx
```ts
import { defineConfig } from 'astro/config'
import react from '@astrojs/react'

export default defineConfig({
  integrations: [
    react(),  // [!code focus]
    mdx(),    // [!code focus]
  ]
})
```
````

#### Error highlighting

Mark a line as erroneous — renders in red, distinct from a diff:

````mdx
```ts
const port = process.env.PORT
app.listen(port)          // [!code error]
app.listen(Number(port))  // [!code highlight]
```
````

#### Combining annotations

Multiple annotation types can coexist in the same block:

````mdx
```ts
function connect(url: string) {
  const oldClient = createClient(url)    // [!code --]
  const newClient = createClient(url, {  // [!code ++]
    retry: true,                         // [!code ++]
    timeout: 5000,                       // [!code ++]
  })                                     // [!code ++]
  return newClient                       // [!code focus]
}
```
````

#### Language icons

Over 60 languages are supported and display their icon automatically from the language identifier. No configuration needed.

**Smart detection for shell blocks**: a `bash` block starting with `pnpm install` shows the pnpm icon instead of a generic shell icon. Supported commands: `npm`, `npx`, `pnpm`, `yarn`, `bun`, `cargo`.

---

### Cheat sheets

#### Components

| Component | Opening syntax | Colons | Closes with |
|-----------|----------------|--------|-------------|
| Callout | `:::callout{variant="info"}` | 3 | `:::` |
| Callout with title | `<Callout variant="info" title="...">` | — | `</Callout>` |
| Card Group | `::::card-group{cols=2}` | 4 | `::::` |
| Card | `:::card{label="..." icon="lucide:..." href="..."}` | 3 | `:::` |
| Step Group | `::::step-group` | 4 | `::::` |
| Step | `:::step{title="..."}` | 3 | `:::` |
| Code Group | `:::codegroup` | 3 | `:::` |
| Tabs | `<Tabs items={[...]} client:load>` | — | `</Tabs>` |
| Tab | `<Tab label="...">` | — | `</Tab>` |

#### Code block annotations

| Feature | Syntax |
|---------|--------|
| File label | ` ```ts [src/file.ts] ` |
| Highlight line (inline) | `// [!code highlight]` |
| Highlight lines (meta) | ` ```ts {1,3-5} ` |
| Highlight word | `// [!code word:name]` |
| Diff add | `// [!code ++]` |
| Diff remove | `// [!code --]` |
| Focus | `// [!code focus]` |
| Error | `// [!code error]` |
