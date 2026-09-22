# Explainer MDX Reference (blog subset)

This file documents the MDX components and code-block features a long-form blog article actually uses: Callout, Card Group + Card, Steps, Code Group, Tabs, and the full set of code-block annotations. It is deliberately the blog subset — documentation-only concerns are out of scope and are not covered here. All components are auto-imported in every `.mdx` file, so never write an import statement.

---

## Contents

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

## Nesting and colon counts

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

## Callout

Colored boxes with icons for highlighting important content. Content supports full Markdown.

### Variants

| Variant | Color | Icon | Use for |
|---------|-------|------|---------|
| `info` | Blue | ℹ️ | General tips, notes (default) |
| `success` | Green | ✓ | Positive outcomes, confirmations |
| `warning` | Yellow | ⚠️ | Cautions, deprecations |
| `danger` | Red | ✕ | Critical warnings, breaking changes |

### Directive syntax (preferred)

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

### JSX alternative — required for a title

The `title` attribute works only in JSX syntax.

```mdx
<Callout variant="info" title="Prerequisites">
  Make sure you have Node.js 22+ installed.
</Callout>
```

### Props

| Prop | Type | Default | Note |
|------|------|---------|------|
| `variant` | `"info"` \| `"success"` \| `"warning"` \| `"danger"` | `"info"` | State it explicitly |
| `title` | `string` | — | JSX syntax only |

### Gotchas

- **`title` in directive syntax is silently ignored.** `:::callout{variant="info" title="My Title"}` renders without the title and gives no error. Use `<Callout variant="info" title="...">` when you need one.
- **Missing closing `:::`.** The directive never closes; everything after it is swallowed into the callout or breaks the page.
- **Wrong colon count.** `::callout{...}` with two colons is not recognized. Directives require 3+ colons.
- **Invalid variant value.** `variant="note"` is not valid. Only `info`, `success`, `warning`, `danger`.
- **Faking a title with bold text.** `**Warning:** ...` as the first line works visually but is just bold content, not a semantic title. Use the JSX `title` prop instead.
- **Import statements.** Never write `import { Callout } from '@explainer/mdx'`. All components are auto-imported.

---

## Card Group + Card

Responsive grid for related content, links, or further reading. `::::card-group` (4 colons) contains `:::card` (3 colons). Card content supports full Markdown, and `href` makes the entire card clickable.

### Syntax

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

### CardGroup props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `cols` | `1` \| `2` \| `3` \| `4` | `2` | Max columns in the grid |

### Card props

| Prop | Type | Required | Description |
|------|------|----------|-------------|
| `label` | `string` | Yes | Card title |
| `icon` | `string` | No | Iconify identifier, e.g. `lucide:rocket` |
| `href` | `string` | No | Makes the card a clickable link |

**Finding icons**: browse [icon-sets.iconify.design](https://icon-sets.iconify.design). Common sets: `lucide:`, `devicon:`, `mdi:`.

### Gotchas

- **`card-group` with 3 colons.** Both directives end up at the same nesting level and the parser does not recognize the group. The container needs 4 (`::::`).
- **`card` with 4 colons.** It competes with the parent container and breaks nesting. Cards need exactly 3 (`:::`).
- **Missing `label`.** It is required; without it the card does not render correctly.
- **`cols` as a quoted string.** Write `{cols=2}`, not `{cols="2"}` — it takes a bare number.
- **A standalone `:::card`.** Cards must always be nested inside a `::::card-group`. A card outside a group is not a supported pattern.
- **Icon without a set prefix.** `icon="rocket"` does not resolve. Use `lucide:rocket`, `devicon:typescript`, `mdi:home`.

---

## Steps

Sequential instructions with automatic numbering and a vertical progress indicator. `::::step-group` (4 colons) contains `:::step` (3 colons). Nesting other components — callouts, code blocks — inside a step is fully supported.

### Syntax

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

### Props

**step-group** — no props, acts as container.

| step prop | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | `string` | Yes | Displayed next to the step number |

### Gotchas

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

## Code Group

Multiple code blocks as switchable tabs. Lighter than `Tabs` for pure code comparison — directive-based, no JSX, no `client:load`. `:::codegroup` (3 colons) wraps standard fenced code blocks, and every block needs a `[label]` in its meta string to generate a tab. The label is also shown as the file label in the code block header.

### Syntax

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

### Gotchas

- **Code blocks without `[label]`.** The tabs have no title and the group does not render properly. Every block inside a `:::codegroup` must carry a `[label]`.
- **Wrong directive name.** It is `:::codegroup`, one word, no hyphen. `:::code-group` is not recognized.
- **JSX children inside a code group.** `:::codegroup` expects raw fenced code blocks, not `<Tab>` elements. Mixing the two syntaxes does not work.
- **A single code block.** A group with one block is a plain code block with extra markup. Use a plain fenced code block instead.
- **Special characters in labels.** `[src/index.ts (main entry)]` may cause unexpected behavior. Keep labels short and simple: `[src/index.ts]`, `[TypeScript]`.

---

## Tabs

Switchable tab panels. `Tabs` and `Tab` are React components: JSX syntax only, and they require the `client:load` Astro directive.

### Syntax

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

### Props

**Tabs**

| Prop | Type | Description |
|------|------|-------------|
| `items` | `string[]` | Tab labels displayed in the header |
| `client:load` | — | Astro directive; always required |

**Tab**

| Prop | Type | Description |
|------|------|-------------|
| `label` | `string` | Must match an entry in `items` |

### Tabs vs Code Group

If the content is purely code blocks with no surrounding text, prefer `:::codegroup` — it is simpler, directive-based, and needs no `client:load`. Use `<Tabs>` when tabs contain mixed content: text plus code, or multiple sections.

### Gotchas

- **Missing `client:load`.** Astro does not hydrate the component. The tabs render as static HTML and clicking them switches nothing.
- **Label does not match `items`.** Labels are case-sensitive: `label="Npm"` does not match `"npm"`. The tab either fails to render or shows an incorrect active state.
- **Directive syntax.** There is no `:::tabs` equivalent. `Tabs` and `Tab` only work as JSX.
- **`items` as a string.** It must be a JavaScript array: `items={["pnpm", "npm", "yarn"]}`. A plain string does not parse into tabs.
- **Mismatched `<Tab>` count.** If `items` declares 3 tabs but only 2 `<Tab>` children exist, the third header renders and shows nothing when clicked. Match the count exactly.

---

## Code block features

Standard fenced code blocks with a language identifier get syntax highlighting automatically (Shiki, dual light/dark themes). Everything below is opt-in via inline annotations or meta string options.

### File label

Add `[path/to/file]` in the meta string to display a label in the code block header.

````mdx
```ts [src/index.ts]
import { createApp } from './app'
const app = createApp()
```
````

Labels work with any language. The same label is used as the tab title inside a `:::codegroup`.

### Line highlighting

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

### Word highlighting

Highlight every occurrence of a word. The comment itself is hidden from output:

````mdx
```ts
// [!code word:config]
const config = loadConfig()
validateConfig(config)
applyConfig(config)
```
````

### Diffs

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

### Focus mode

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

### Error highlighting

Mark a line as erroneous — renders in red, distinct from a diff:

````mdx
```ts
const port = process.env.PORT
app.listen(port)          // [!code error]
app.listen(Number(port))  // [!code highlight]
```
````

### Combining annotations

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

### Language icons

Over 60 languages are supported and display their icon automatically from the language identifier. No configuration needed.

**Smart detection for shell blocks**: a `bash` block starting with `pnpm install` shows the pnpm icon instead of a generic shell icon. Supported commands: `npm`, `npx`, `pnpm`, `yarn`, `bun`, `cargo`.

---

## Cheat sheets

### Components

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

### Code block annotations

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
