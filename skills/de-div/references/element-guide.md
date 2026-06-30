# Semantic element guide

The reference catalog for de-divving. Use it to pick the right element by **meaning**, and to know
what default browser styling a swap introduces so visual appearance is preserved. SKILL.md drives
the workflow; this file is the lookup.

Two golden rules from the HTML spec frame everything:

- **`<div>` is the element of last resort** — "no special meaning at all… an element of last resort,
  for when no other element is suitable." It is legitimate for pure layout/styling/scripting hooks.
- **`<span>` is the inline `<div>`** — no meaning. But most spans in real code are actually `strong`,
  `em`, `b`, `i`, `code`, `time`, `abbr`, `data`, `cite`, etc. used as a styling hook.

**Mindset: ask what the content *is*, not what it looks like.** Pick by meaning; restyle with CSS.
Bold/italic/indent defaults are historical artifacts, not the element's purpose.

---

## 1. Inline elements — `<span>` replacements

For each: meaning / when to use instead of a span, and the default UA styling you inherit.

| Element | Meaning / when to use instead of `<span>` | Default UA styling |
|---|---|---|
| `<em>` | Vocal stress emphasis — **changes sentence meaning** | `font-style: italic` |
| `<strong>` | Importance / seriousness / urgency; warnings, "Do not…" | `font-weight: bold` |
| `<b>` | Draw attention, no importance, no alt voice: keyword, product name, lede | `font-weight: bold` |
| `<i>` | Alternate voice/mood: foreign phrase, taxonomic/technical term, thought, ship name | `font-style: italic` |
| `<mark>` | Highlighted because relevant to the reader *now* (search match) | `background: yellow; color: black` |
| `<small>` | Side comments / fine print / legal / copyright | `font-size: smaller` (~83%) |
| `<s>` | Content no longer accurate or relevant (old price) | `text-decoration: line-through` |
| `<del>` / `<ins>` | An edit: removed / added content (often with `datetime`/`cite`) | line-through / underline |
| `<u>` | Unarticulated **non-textual** annotation: misspelling, proper-name mark | `text-decoration: underline` ⚠ looks like a link |
| `<cite>` | Title of a work (book/film/song/paper). **Not** a person's name | `font-style: italic` |
| `<q>` | Short **inline** quotation — UA inserts the quote marks, don't type them | quote marks via `::before/::after` |
| `<abbr>` | Abbreviation/acronym; expansion in `title` | ~none in modern UAs |
| `<dfn>` | The **defining instance** of a term (ancestor must contain the definition) | `font-style: italic` |
| `<data>` | Human text + machine-readable `value` (product code, rating) | none — safe span swap |
| `<time>` | Date/time/duration + machine-readable `datetime` | none — safe span swap |
| `<code>` | Fragment of computer code, element name, filename | `font-family: monospace` |
| `<kbd>` | User input — keys typed/spoken (nestable) | `font-family: monospace` |
| `<samp>` | Sample **output** from a program/system | `font-family: monospace` |
| `<var>` | A variable / placeholder (math, code, prose placeholder) | `font-style: italic` |
| `<sub>` / `<sup>` | Semantic sub/superscript: chemistry, math, footnote refs, ordinals | `vertical-align` + `font-size: smaller` |
| `<output>` | Result of a calculation / user action | none — safe span swap |
| `<span>` | **Only** when nothing semantic applies — pure styling/scripting hook | none |

### The four "emphasis-like" elements — the most-misunderstood corner of HTML

`<b>` does **not** mean bold and `<i>` does **not** mean italic. They are real semantic elements;
the bold/italic rendering is just the default style. Disambiguate by intent:

| You mean… | Element | Changes sentence meaning? | Default style |
|---|---|---|---|
| Vocal **stress** on a word | `<em>` | **Yes** | italic |
| **Importance** / seriousness / urgency | `<strong>` | No | bold |
| **Relevance to the reader right now** (highlight/match) | `<mark>` | No | yellow bg |
| **Draw attention** — keyword, product name, lede | `<b>` | No | bold |
| **Alternate voice/mood** — foreign, taxonomic, term, thought | `<i>` | No | italic |
| Genuinely nothing semantic, pure styling | `<span>` + CSS | No | — |

- `<em>` *changes meaning*: "**Cats** are cute" vs "Cats **are** cute". Nesting raises stress.
- `<strong>` = serious/urgent, doesn't change meaning. Nesting raises relative importance.
- `<b>` = attention without importance/voice. MDN: "do **not** use `<b>` for styling or importance."
  Best practice: add a class saying *why* — `<b class="lead">`, `<b class="product-name">`.
- `<i>` = a different *quality* of text. Add `lang` for foreign phrases.

```html
<p><b class="lead">In this article we replace div soup with semantic HTML.</b>
   The <b class="keyword">b</b> element draws attention without adding importance.</p>
<p>The <b>Acme Widget 3000</b> beat the <b>MegaGadget Pro</b>.</p>
<p>The Latin <i lang="la">Veni, vidi, vici</i> describes <i>Homo sapiens</i> at its most decisive.</p>
<p>Was <s>$99.99</s>, now <strong>$49.99</strong>.</p>
<p>Press <kbd>Ctrl</kbd>+<kbd>R</kbd>; <code>reload()</code> returns <samp>ok</samp>.</p>
<p>Published <time datetime="2026-06-30">June 30, 2026</time>.</p>
<p>Caffeine is C<sub>8</sub>H<sub>10</sub>N<sub>4</sub>O<sub>2</sub>.</p>
```

**A `<span>` is sometimes correct.** When text has no meaning of its own *and* no more-specific
element is legal in context, keep the span. Classic case: an inline form-error inside a `<p>` — a
`<p>` can't nest another `<p>`, so the error is a `<span aria-describedby-target>`. Don't manufacture
false semantics; a plain styling hook is a legitimate span/div.

---

## 2. Block elements — `<div>` replacements

### Decision flow when converting a `<div>`

1. Standalone, syndicatable/reusable unit (could be republished on its own)? → **`<article>`**
2. Thematic chunk with a heading that belongs in the document outline? → **`<section>`**
3. Tangential/separable sidebar-type content? → **`<aside>`**
4. Major navigation block → **`<nav>`** · intro/header region → **`<header>`** ·
   footer region → **`<footer>`** · the page's dominant unique content → **`<main>`**
5. List of things → **`<ul>`/`<ol>` + `<li>`** · name/value pairs → **`<dl>`/`<dt>`/`<dd>`** ·
   figure + caption → **`<figure>`/`<figcaption>`** · quote block → **`<blockquote>`**
6. Collapsible → **`<details>`/`<summary>`** · modal → **`<dialog>`** ·
   form-control group → **`<fieldset>`/`<legend>`** · clickable → **`<button>`** ·
   contact info → **`<address>`**
7. None of the above — pure styling/layout hook? → **keep `<div>`** (last resort, legitimately)

### `<article>` vs `<section>` vs `<div>`

- **`<article>`** — complete, self-contained, in-principle reusable/syndicatable composition: blog
  post, news story, comment, **product/UI card**, login widget. Litmus: *could this be pulled out and
  republished on its own?*
- **`<section>`** — a thematic grouping, **typically with a heading**. Spec: NOT a generic container;
  "when an element is needed only for styling or scripting, use the `div` element instead." Rule of
  thumb: appropriate only if its contents would appear in the document **outline** (it has/warrants a
  heading).
- **`<div>`** — no meaning; last resort for styling/scripting hooks only.
- **`<aside>`** — tangential, separable content (sidebar, pull quote, related links, ads). Not for
  inline parentheticals.

### Sectioning / landmark elements

| Element | Meaning | Notes |
|---|---|---|
| `<nav>` | Major navigation block | Not *every* group of links (not footer boilerplate) |
| `<header>` | Intro/navigational aids for nearest section or page | Not sectioning content |
| `<footer>` | Footer for nearest sectioning ancestor or `<body>` | Not sectioning content |
| `<main>` | The document's dominant content | **At most one** visible per page |
| `<address>` | Contact info for nearest `<article>` or `<body>` | Contact info *only*; italic by default |
| `<hgroup>` | A heading + `<p>` subheading/tagline | |
| `<search>` | Region of search/filter controls (a landmark) | Modern replacement for `<form role="search">` |

### Grouping content

- **`<figure>` / `<figcaption>`** — self-contained referenced content (image, diagram, code listing,
  poem) + optional caption. `<figcaption>` must be first or last child.
- **`<dl>` / `<dt>` / `<dd>`** — an **association / description list** of name-value groups (not just
  term→definition): metadata key/value pairs, Q&A, definitions, any name-value data. A group is 1+
  `<dt>` (names) then 1+ `<dd>` (values); wrap each group in a `<div>` for styling if needed.
- **`<blockquote>`** — a quoted section (optional `cite` URL). Attribution goes *outside* (e.g. a
  following `<figcaption>`).
- **`<ul>` / `<ol>` / `<li>`** — replace any "list of sibling divs." `<ul>` = order irrelevant;
  `<ol>` = order meaningful (`reversed`, `start`, `<li value>`).
- **`<menu>`** — semantic `<ul>` alternative for a toolbar/list of commands.
- **`<p>`, `<pre>`, `<hr>`** — paragraph; preformatted text; paragraph-level thematic break.

```html
<dl>
  <div><dt>Name</dt><dd>Ada Lovelace</dd></div>
  <div><dt>Field</dt><dd>Mathematics</dd><dd>Computing</dd></div>
</dl>

<figure>
  <blockquote cite="https://example.com"><p>It was a bright cold day in April.</p></blockquote>
  <figcaption>— <cite>Nineteen Eighty-Four</cite>, George Orwell</figcaption>
</figure>
```

---

## 3. ⚠ Default browser styling — accounting for appearance

A semantic element carries user-agent CSS that a `<div>`/`<span>` doesn't. When CONVERTing, the job
is to keep the page looking the same; when AUTHORing, the job is to write the element's CSS knowing
these defaults. Either way the element choice should never silently change the look. Two buckets:

### Bucket A — SAFE drop-ins (no visual surprise; just `display:block` or inline)

Pure semantic upgrades with nothing to undo — swap freely, no CSS needed:

- Block: `<article>`, `<section>`, `<nav>`, `<main>`, `<header>`, `<footer>`, `<aside>`, `<hgroup>`,
  `<search>`, `<figcaption>` → all just `display: block`.
- Inline: `<data>`, `<time>`, `<abbr>`, `<output>` → no visual change.

These are the **ideal first targets**: replace `<div>` with zero CSS risk.

### Bucket B — brings UA styling you must reset/account for

Whenever you use a Bucket-B element: in CONVERT mode add the neutralizing CSS (if a stylesheet is in
scope) or document the needed rule in the report; in AUTHOR mode write the element's CSS with these
defaults in mind. Never let an element choice silently change the look.

**Inline (usually just font tweaks):** `<b>`/`<strong>` → bold; `<i>`/`<em>`/`<cite>`/`<dfn>`/`<var>`/
`<address>` → italic; `<s>`/`<del>` → line-through; `<u>`/`<ins>` → underline (⚠ link-like; consider
`text-decoration: red wavy underline` for a misspelling); `<mark>` → yellow bg; `<small>` → smaller;
`<sub>`/`<sup>` → baseline shift + smaller; `<code>`/`<kbd>`/`<samp>`/`<pre>` → monospace; `<q>` →
auto quote marks.

**Block / grouping (margins, indent, list markers):**

| Element | Default UA styling to account for | Typical neutralizer |
|---|---|---|
| `<blockquote>`, `<figure>` | `margin: 1em 40px` (big indent) | `margin: 0` |
| `<ul>` / `<ol>` | list marker + `padding-inline-start: ~40px` + `margin-block: 1em` | `list-style: none; padding: 0; margin: 0` |
| `<dd>` | `margin-inline-start: 40px` | `margin: 0` |
| `<dl>`, `<p>` | `margin-block: 1em` | `margin: 0` |
| `<address>` | `font-style: italic` | `font-style: normal` |
| `<hr>` | beveled `border: inset` + auto margins | flat `border-top` |

**Interactive / form elements — the big gotchas:**

- **`<fieldset>`** — `border: 2px groove`, padding, `margin-inline: 2px`, `min-inline-size:
  min-content`. To behave like a plain `<div>`: reset `border`, `padding`, `margin`, and
  `min-inline-size: auto`. (Canonical contrast: heavy default styling, vs. style-free `<article>`.)
- **`<legend>`** — special box straddling the fieldset's top border; restyle if undesired.
- **`<details>` / `<summary>`** — `<details>` is **collapsed by default** (content hidden until the
  `open` attribute; toggle + keyboard are built in, no JS). `<summary>` shows a disclosure triangle
  (`display: list-item` + `::marker`). Restyle/remove the marker and add `cursor: pointer` to match a
  custom design.
- **`<dialog>`** — **`display: none` until opened** via `.showModal()`/`.show()`. Modal dialogs are
  UA-centered, get a `::backdrop`, sit in the top layer, make the background inert, set focus, and
  close on `Esc` — all free. Replacing a div modal deletes your centering math, backdrop element,
  focus-trap JS, and Esc handler.
- **`<button>`** — full native chrome (`appearance: auto`, border, `ButtonFace`, `cursor: pointer`).
  Correct semantics/keyboard/focus for free; usually reset `appearance`/`border`/`background`/
  `padding`/`font` to match the old look (often `all: unset` + your styles).
- **`<progress>` / `<meter>`** — native widgets; need `appearance: none` + vendor pseudo-elements to
  style (`::-webkit-progress-bar`, `::-moz-progress-bar`, …).

---

## 4. Native HTML over div + ARIA

The accessibility case. A native element ships with the role, keyboard handling, focus management,
and states wired up by the browser. A `<div>` + ARIA gives you only the *label* and forces you to
hand-build the rest in JS — which is where a11y bugs come from.

**First Rule of ARIA** (w3.org/TR/using-aria): "If you can use a native HTML element or attribute
with the semantics and behavior you require already built in, instead of re-purposing an element and
adding an ARIA role/state/property, then do so." Reinforced by "No ARIA is better than Bad ARIA" and
"A role is a promise" (WAI-ARIA APG, Read Me First) — and by analogy, *bad semantics are worse than a
neutral div*, so prefer keeping a `<div>` over guessing a wrong element.

### Patterns with a native element to prefer over div+ARIA

| Pattern | div soup it replaces | Native replacement |
|---|---|---|
| Disclosure (show/hide) | `div[role=button][aria-expanded]` + JS keys + hidden div | `<details>`/`<summary>`, or real `<button aria-expanded>` |
| Accordion | `div[role=button]` headers + manual keys | stack of `<details>`; ARIA accordion only for single-open |
| Modal dialog | `div[role=dialog][aria-modal]` + focus trap + Esc + inert | native `<dialog>` + `.showModal()` |
| Button | `div[role=button][tabindex=0]` + Enter/Space handlers | `<button>` (add `aria-pressed` only for toggles) |
| Link | `span[role=link][tabindex=0][onclick]` | `<a href>` |
| Breadcrumb | non-semantic divs | `<nav aria-label="Breadcrumb"><ol><li><a aria-current="page">` |

```html
<!-- div soup -->
<div role="button" tabindex="0" aria-expanded="false" aria-controls="c1">Details</div>
<div id="c1" hidden>…</div>
<!-- native: zero JS, zero ARIA, keyboard + state built in -->
<details><summary>Details</summary><p>…content…</p></details>
```

> ⚠ These conversions change runtime behavior and JS wiring (event handlers, open/close logic, focus
> management). Treat them as **behavior-changing** — confirm with the user and check the JS, don't
> silently rewrite a working widget. See SKILL.md "act vs ask."

### Landmarks → native elements (no `role=` on a div)

| ARIA landmark | Native element | Condition |
|---|---|---|
| `banner` | `<header>` | only when **top-level** (direct child of `<body>`) |
| `navigation` | `<nav>` | always implicit |
| `main` | `<main>` | always; one per page |
| `complementary` | `<aside>` | always implicit |
| `contentinfo` | `<footer>` | only when **top-level** |
| `region` | `<section>` | only with an **accessible name** (`aria-label`/`aria-labelledby`) |
| `form` | `<form>` | only with an **accessible name** |
| `search` | `<search>` | always implicit |

Labeling: repeated landmark roles need *distinct* `aria-label`s; don't repeat the role name in the
label ("Site", not "Site Navigation Navigation"). Aim for ≤ ~7 landmarks; all content inside one.

### When ARIA IS correct (no native element)

Build these on native `<button>`s and fulfill the full keyboard/focus promise: **Tabs**
(`role=tablist/tab/tabpanel` + roving tabindex + arrows), **Menu button / application menu**
(`aria-haspopup` + `role=menu/menuitem`; native `<select>` covers value-picking), **Alert / live
regions** (`role=alert` implies `aria-live=assertive`), **Combobox, Listbox, Tree, Grid, Slider
beyond `<input type=range>`, Spinbutton, Toolbar, Tooltip, Carousel, Switch.** Check for a native
input type (`range`, `color`, `date`, …) or `<progress>`/`<meter>` before reaching for ARIA.

---

## 5. Component-level substitution table

For de-divving whole components, not just single elements:

| UI pattern (typical div soup) | Semantic authoring |
|---|---|
| Card | `<article>` + inner `<header>`/`<h2>`/`<footer>`; actions in a `<nav>` or `<footer>` |
| Card grid / list | `<ul>`/`<ol>` of `<li>` each wrapping an `<article>` |
| Page shell | `<header>` (banner) · `<nav>` · `<main>` · `<aside>` · `<footer>` (contentinfo) |
| Site / section navigation | `<nav aria-label>` + `<ul>`/`<li>`/`<a>` |
| Breadcrumb | `<nav aria-label="Breadcrumb"><ol><li><a aria-current="page">` |
| Modal / dialog | native `<dialog>` + `.showModal()` |
| Dropdown menu (commands) | `<button>` + `<menu>`/`<li>`, or ARIA menu if rich |
| Accordion / disclosure | `<details>`/`<summary>` (ARIA accordion only for single-open) |
| Tabs | ARIA `role=tablist/tab/tabpanel` on native `<button>`s (no native element) |
| Form field group | `<form>` + `<fieldset>`/`<legend>` + label/field pairs |
| Inline field error | `<span aria-live>` + `aria-describedby` |
| Error summary | `<section role="alert">` + `<ul>` of `<a href="#field">` |
| Key/value data display | `<dl>`/`<dt>`/`<dd>` |
| Figure with caption | `<figure>`/`<figcaption>` |
| Quote with attribution | `<figure>` > `<blockquote>` + `<figcaption><cite>` |
| Search box | `<search>` > `<form>` |
| Progress / spinner | `<progress>` |
| Data table | `<table>` family (never a grid of divs) |
| Badge / toast / tooltip | no native element — a styled `<span>`/`<div>` is acceptable here |

---

## 6. Worked component examples

**Card** (replaces `<div class="card">` with `.card-header`/`.card-footer` divs):
```html
<article class="card">
  <header><h2>Order #123</h2></header>
  <p>Large pepperoni pizza.</p>
  <footer><a href="/reorder/123">Order this again</a></footer>
</article>
```

**Card list** — order meaningful → `<ol>`; otherwise `<ul>`:
```html
<ol class="orders">
  <li><article>…</article></li>
  <li><article>…</article></li>
</ol>
```

**Self-contained login** (the whole thing is reusable → `<article>`):
```html
<article class="login">
  <header><h2>Login</h2></header>
  <form>
    <p><label for="username">Username</label>
       <input type="text" id="username" name="username"></p>
    <p><label for="password">Password</label>
       <input type="password" id="password" name="password"></p>
  </form>
  <footer><a href="/register">Register</a></footer>
</article>
```

**Grouped controls** — `<fieldset>` + `<legend>` (remember to reset the default border/padding):
```html
<fieldset>
  <legend>Pizza toppings</legend>
  <p><input type="checkbox" id="pepperoni"><label for="pepperoni">Pepperoni</label></p>
  <p><input type="checkbox" id="sausage"><label for="sausage">Sausage</label></p>
</fieldset>
```

---

## 7. Notes / caveats

- The **"First Rule of ARIA"** wording is from the W3C *Using ARIA* note, not the APG pattern pages
  (those document the manual ARIA recipe). The native `<dialog>`/`<details>` recommendation is the
  correct *application* of the rule.
- `<b>`/`<i>`/`<u>`/`<mark>`/`<kbd>`/`<samp>`/`<sub>`/`<sup>` definitions follow MDN, which quotes the
  spec closely. Default UA pixel values (`40px`, `1em`) are representative and vary across browsers.
- **Breadcrumbs as a list vs. a hierarchy:** the WAI-ARIA APG uses `<nav><ol><li>`. Tony Alicea's
  HTML Recipes deliberately reject the list, treating breadcrumbs as a hierarchy of links in a `<p>`
  inside `<nav>`. Both are defensible — surface it as a decision rather than silently picking one.

### Sources

- WHATWG HTML Living Standard — text-level-semantics / grouping-content / sections
- MDN HTML element reference — developer.mozilla.org/en-US/docs/Web/HTML/Element/
- W3C WAI-ARIA APG — w3.org/WAI/ARIA/apg/ · Using ARIA — w3.org/TR/using-aria/
- BeerCSS ELEMENTS — github.com/beercss/beercss/blob/main/docs/ELEMENTS.md
- Tony Alicea's HTML Recipes — tonyalicea.dev/projects/html-recipes/
