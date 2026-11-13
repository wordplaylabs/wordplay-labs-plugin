{{!-- How to write for the page (bound into wordplay-guide.md as `editor`).
      The ground truth is the student's frame: packages/player/src/lib/
      frame-document.ts (math, the policy) and the runtime's stylesheet
      (scripts/runtime-build.ts). Figures are SVG since 1 October (Javi:
      "do svg figures"). --}}
How to write for the page, which is a browser's own, in a frame of its own:

- **Text** is plain HTML: `<p>`, `<h1>` to `<h4>`, `<ul>` and `<ol>`,
  `<table>`, `<blockquote>`, `<em>`, `<strong>`, `<code>`, `<a>`. The page
  dresses it in the site's look, light and dark: style none of it.
- **Math** is `$…$` in the text, and `$$…$$` displayed, drawn with KaTeX:
  `aligned`, `pmatrix` and `cases` work; no theorem environments, `\label`
  or `\ref`. Never inside `<code>`, which shows it as written.
- **Figures** are SVG, inline: one `<svg>` with a `viewBox` and no width or
  height, a `data-description` saying what it shows, with its numbers
  (below), labels as `<text>`. Lines and labels in `currentColor`, the page's ink, and
  `var(--logo-front)`, the logo's teal, for the one thing the figure is
  about; a color only for what it means in the subject (`var(--success)`,
  `var(--danger)`, `var(--ink-muted)`). Leave shapes unfilled, or tint them with
  `fill-opacity="0.15"`; never a white or pale fill, which hides a label in
  the dark.
- **Pictures** cannot be drawn: ask the teacher for one and place it with
  `<figure><img src="address" alt="what it shows"><figcaption>…</figcaption></figure>`.
- **Every element you build carries its description**: the tutor never sees
  its HTML, only the `data-description` on its outermost element, what it is
  and how it can change (by the student, its script or the tutor), at most
  {{maxDescription}} characters. **One that can change has a `data-state`**,
  what it shows now in at most {{maxState}} characters, which its script
  rewrites the moment that changes (`element.dataset.state = "2 of 3 hints
  shown"`). Never the answer in either. Name each control: the tutor presses
  it by its name. Text, a picture, an answer and a button need none;
  decoration has `aria-hidden="true"`.
- **Scripts** are inline, or from `cdn.jsdelivr.net/npm/`; the page reaches
  no network, posts no form, and keeps nothing between visits of its own;
  what it hands `document.wordplay.keep` comes back ("Scripts"). What you
  draw yourself takes the page's colors as above, and nothing moves by
  itself for a student who asked for less motion
  (`prefers-reduced-motion`).
