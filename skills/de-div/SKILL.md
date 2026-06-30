---
name: de-div
description: >-
  Author semantic, accessible HTML and keep "div soup" (meaningless <div>/<span>) out of the DOM — in two modes. EDIT mode de-divs existing markup (a codebase, page, component, or template), turning divs/spans into real semantic elements while preserving the visual appearance. CREATE mode rides along while you write new markup, steering it toward semantic elements from the start. Works on anything that produces DOM — HTML, JSX/TSX, Vue, Svelte, Astro, Angular templates, PHP, Express, etc. In both modes the skill does the work, acts as a thinking partner on ambiguous cases, accounts for default element styling, and ends with an educational report teaching the user to be a good HTML author. Use whenever the user wants to remove or reduce divs/spans, "de-div" or "de-div soup" their markup, make HTML more semantic or accessible, replace div/span with real elements, audit markup for semantics, OR wants to create/scaffold/build a new page, component, or template and have the markup come out semantic and accessible — even if they don't say "de-div." Also trigger when reviewing or writing a component whose markup is (or would default to) nested divs.
---

# de-div

Help the user be an **HTML author** — someone who chooses elements for *meaning*, not by defaulting to `<div>` and `<span>`. Whatever the mode, you do three things at once:

1. **Do the work** — produce semantic markup wherever the meaning is clear.
2. **Be a thinking partner** — where the content's meaning is genuinely ambiguous, don't guess; log it and decide *with* the user, in logical groups.
3. **Teach** — finish with a report explaining the *types* of semantic choices made and why, so the user learns the principles, not just gets a diff.

A catalog of useful elements and patterns, default-styling buckets, native-over-ARIA patterns, and component examples live in **`references/ELEMENT-GUIDE.md`**. Read it when you need to pick a specific element or check what default styling an element carries, but you can also rely on your own knowledge of semantic HTML. Internalize the workflow below:

---

## Two modes

Same skill, two entry points. Detect which one you're in and start accordingly; the two share the same spine (decide by meaning → log what's ambiguous → resolve with the user → report).

- **EDIT** — the markup already exists and you're refactoring it. Triggers: "de-div this," "make this semantic," "too many divs," pointing at a file/component/codebase. The constraint is **preserve the existing look and behavior** while swapping in real elements.
- **CREATE** — the markup doesn't exist yet and you're writing it. Triggers: "build/scaffold/create a page/component/template" while this skill is active. There's no existing look to preserve; instead you **reach for the right element as you write**, and account for the default styling of what you author in the CSS you write alongside it.

If both apply (e.g. "add a new section to this component and clean up the divs while you're there"), run them together: CREATE the new markup semantically and EDIT the surrounding soup, with one shared working list and one report.

**One deliverable.** The *only* file you write into the user's project is **`de-div-report.md`**, at the very end. Track ambiguities and decisions in an **ephemeral working list** — your task/todo list, or a scratch file in a temp location *outside* the project. Never write a decisions file into the project; the report (authored last, when every answer is known) is the durable record, and its "Decisions" section absorbs what the working list held.

If it's unclear if you are creating or editing, ask the user to clarify. If they don't know, default to EDIT mode (you can always add new markup later).

---

## Core principles (both modes)

- **Ask what the content *is*, not what it looks like.** `<strong>` is not "bold," `<em>` is not "italic," `<section>` is not "a box." Pick by meaning, style with CSS.
- **`<div>` and `<span>` are legitimate** as pure layout/styling/scripting hooks. The goal is to keep *meaningless* divs out where a real element fits — not to eliminate every div. A wrong semantic element is worse than a neutral div (it lies to assistive tech), so **prefer a div over guessing**.
- **Account for default styling.** Semantic elements often carry user-agent CSS. In EDIT mode that means preserving the existing look (apply a neutralizing rule or document it); in CREATE mode it means writing the element's CSS with its defaults in mind. Either way, an element choice should never silently change how the page looks. See the Bucket A / Bucket B split in the guide.
- **Respect the host language.** JSX uses `className`/`htmlFor` and camelCase handlers; Vue/Svelte/Angular have their own binding syntax; fragments and component boundaries matter. Author in the idiom of the file — a semantic element is still semantic as a JSX tag.

---

## Workflow

The spine is shared. Only the first two steps differ by mode; **steps 3–5 are identical.**

### EDIT · Step 1 — Scope and set up

- Confirm what's in scope: a single file, a component, a directory, or a whole site. If the user dropped in one file or component, that's the scope — don't wander.
- Identify the templating language(s) so edits respect syntax.
- Find the CSS that styles the target markup (stylesheets, CSS modules, styled-components, Tailwind, `<style>` blocks). You need it to preserve appearance and to know whether you can apply neutralizing rules directly or must document them.
- Inventory the soup with Grep (`<div`, `<span`, `className=`, `role=`) to gauge scale and surface the obvious repeated component patterns.
- Start an **ephemeral working list** for ambiguities and decisions (your task/todo list, or a scratch file in a temp dir *outside* the user's project) and append to it as you go — don't rely on memory across a large codebase, but don't write it into the project either. The single project deliverable, `de-div-report.md`, is written at the very end.

### EDIT · Step 2 — Convert what's clear

Go through the markup. For each `<div>`/`<span>`, recover its meaning from the evidence — **text content, class/id names, existing ARIA roles, surrounding structure, the component's name/purpose** — and apply the decision flows in the guide. Edit when the evidence makes the meaning clear:

- `<div class="card">` with a heading and body → `<article>`.
- Page top region with logo/nav → `<header>`; primary content → `<main>`; bottom/copyright → `<footer>`; a block of nav links → `<nav>`.
- Sibling divs rendered from a list/`map()` → `<ul>`/`<ol>` + `<li>`.
- Repeated `<div><span>Label</span><span>Value</span></div>` → `<dl>`/`<dt>`/`<dd>`.
- `role=`/`aria-*` that names a native element (`role="navigation"` on a div → `<nav>`, drop the role).

Preserve every class, attribute, handler, and binding. After each swap, check the default-styling buckets: **Bucket A** needs nothing; **Bucket B** needs the look preserved — apply the neutralizing CSS if the stylesheet is in scope, and note it for the report. Keep a running tally of the *types* of changes (not every edit — git has those).

### CREATE · Step 1 — Understand what you're building

- Get clear on the thing: what component/page, its content, and (if known) where it lives. You don't need a div inventory — you need to know what the *content means* so you can pick elements for it.
- Note the host language and where the CSS will go (same file, module, Tailwind, etc.) so you author the markup and its styling together.
- Start an **ephemeral working list** (task/todo list or a temp scratch file *outside* the project) for the assumptions and choices you'll make while writing — never a file in the project. Only `de-div-report.md` is delivered, at the end.

### CREATE · Step 2 — Author semantically by default

Write the markup reaching for the right element as you go, instead of emitting divs and "fixing" them later. Lead with the structure from the guide: landmarks (`<header>`/`<nav>`/`<main>`/`<footer>`) for the page shell, `<article>`/`<section>` for self-contained or thematic blocks, lists for collections, `<dl>` for name/value data, real `<button>`/`<a>`/`<details>`/`<dialog>` for interaction, and the meaningful inline elements (`<time>`, `<b>`, `<strong>`, `<code>`, …) for text. Reach for a plain `<div>`/`<span>` only as the deliberate last resort for a pure layout/styling hook.

As you author a Bucket-B element, write its CSS with the default styling in mind (e.g. reset the `<fieldset>` border if your design doesn't want it) so the component looks the way it should out of the gate. When you're forced to make an assumption about content you can't see yet, record it in your working list (see Step 3) rather than burying a silent guess in the output.

### Step 3 — Log ambiguities, don't guess (both modes)

When you **cannot** recover the meaning from the evidence, log it (include the file and line number location) instead of guessing. In EDIT, leave the original in place. In CREATE, emit your best-guess element but flag it. Either way append a structured entry to your working list and keep moving. Cases that should usually become a logged decision:

- **Opaque dynamic content.** `<span>{item.label}</span>`, `{{ value }}`, a slot, an interpolated variable — you can't tell if it's a date, price, code, name, or just text. The right element (`<time>`? `<data>`? `<b>`? plain text?) depends on what it holds. (In CREATE mode this is the most common case: you're authoring a binding whose runtime value you haven't seen.)
- **Bold/italic where intent is unknowable.** `<strong>` (importance) vs `<b>` (keyword/product name) vs decoration? `<em>` (stress) vs `<i>` (term/foreign) vs `<cite>` (work title) vs decoration?
- **`<section>` vs layout `<div>`.** No heading and reads as pure layout → keep/use a div. Plausibly a thematic section but unclear → ask (and whether it needs a heading).
- **Behavior-changing conversions** (EDIT). Clickable div → `<button>`, JS show/hide → `<details>`, custom modal → `<dialog>`. These change runtime behavior, focus, and JS wiring — confirm and inspect the JS before committing; don't silently rewrite a working widget. (See native-over-ARIA in guide.)
- **Genuinely contested choices.** E.g. breadcrumbs as `<nav><ol><li>` (WAI-ARIA) vs. a hierarchy of links in a `<p>` (Tony's HTML Recipes). Surface the trade-off; let the user pick.

Decision entry format (track each as you go, in your working list — not a project file):

```markdown
## [group] short title
- **Mode:** edit | create
- **Where:** path/to/file.jsx:L42  (or "creating: ProductCard price line")
- **Current / planned:** `<span>{order.placedAt}</span>`
- **Question:** What does `placedAt` hold? If a date/time → `<time datetime="…">`; if a label → text.
- **Options:** (a) `<time>` with machine-readable datetime · (b) leave as plain text
- **Assumed for now:** (a)   ← in CREATE mode, what you emitted pending confirmation
- **Default styling impact:** none
```

Group related items (`[inline-emphasis]`, `[dynamic-content]`, `[interactive]`, `[sectioning]`, …) so you can walk through them efficiently later.

### Step 4 — Thought-partner the open decisions (both modes)

Resolve the logged decisions *with* the user — not one popup per item. Batch them by group and present each group together so the user sees the pattern. Use `AskUserQuestion` for clean multiple-choice decisions; fall back to prose when a question needs explanation or there are many similar instances ("all 8 of these wrap a price — should they all be `<data>`?"). Apply each resolution as it's made (in CREATE mode, swap your assumed element for the confirmed one), preserve appearance the same way, and update your working list to record what was chosen — so the report's "Decisions" section comes out accurate. (The resolved decisions live in the report, not a separate file, so nothing can go stale: the report is authored last, once every answer is in.)

If a decision reveals a repeated pattern ("every `{price}` is a formatted currency value"), apply it across all matching instances at once rather than re-asking. For a quick scaffold where the user doesn't want to be interrupted, it's fine to proceed on sensible defaults — but your working list must still record each assumption so nothing is a silent guess and the report can recount it.

### Step 5 — Final report (both modes, teach)

Write **`de-div-report.md`** — the **single file delivered into the user's project**: an educational summary of the *types* of semantic choices made — not a change log. In EDIT mode, "what we changed and why"; in CREATE mode, "how we authored this and why" (the semantic decisions that shaped the markup). Fold the resolved ambiguities from your working list into the "Decisions" section here, so this report is the complete record. Structure:

```markdown
# de-div report

## Summary
One paragraph: what was in scope (or what we built), the overall shape (e.g. "a product page built on
landmark + article structure"), and the headline choices by type.

## What we did, and why
One subsection per *type* of semantic choice that applied (omit those that didn't):

### <Type, e.g. "Landmark structure">
- **What:** divs → / authored as `<header>`/`<main>`/`<nav>`/`<footer>` etc.
- **Why it matters:** screen-reader landmark navigation; clearer document outline.
- **Example (from the code):**
  ```html
  <!-- EDIT: before → after, or CREATE: the chosen markup + the div soup it avoids -->
  ```

(Types that may apply: Landmarks · Sectioning (article/section/aside) · Lists & description lists ·
Inline meaning (b/i/strong/em/mark/time/code…) · Native over ARIA (button/details/dialog/a) ·
Forms (fieldset/legend/label) · Figures & quotes.)

## Appearance & CSS notes
Every place an element carries default browser styling and what was done about it — a reset applied,
or the rule the CSS needs. Group by element. If nothing needed attention, say so.

## Decisions we made together
Brief recap of the ambiguous cases and what was chosen. This report is the record — make it
complete; there is no separate decisions file.

## Where we kept `<div>`/`<span>` on purpose
The legitimate cases — pure layout/styling/scripting hooks, or where a real element would invent false
meaning. Teaches that this is about meaning, not div elimination.

## Principles to carry forward
3–10 short, memorable takeaways drawn from the actual work (e.g. "A card is an `<article>` because it
could be syndicated," "`<b>` draws attention; `<strong>` adds importance — not the same thing").
```

Keep the report tight and concrete — real snippets beat abstract advice. The point is that next time, the user reaches for the right element themselves.

---

## Notes

- **One file out:** the only artifact written into the user's project is `de-div-report.md`. Ambiguity tracking stays in an ephemeral working list (task/todo list or a temp scratch file outside the project) — never a `de-div-decisions.md` in the repo.
- **Scale (EDIT):** work in passes by area — layout/landmarks first (Bucket A, highest value), then components, then inline text. Keep your working list current throughout.
- **Audit only (EDIT):** if the user wants no edits, produce just the report, describing what *would* change (it absorbs the decision log), and skip the editing.
- **Fast scaffold (CREATE):** still author semantically and still log assumptions to your working list — just don't stop to ask on every binding; resolve them in one pass at the end, then write the report.
- Pull `b`/`i`/`strong`/`em` distinctions and default-styling specifics from `references/ELEMENT-GUIDE.md` rather than reciting from memory.