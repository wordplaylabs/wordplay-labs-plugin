{{!-- The creator's builder is sent the builder's guide (wordplay-guide.md,
      for the `creator` host), what the author attached, their other lessons
      and their latest conversation with the tutor (trial.md), and then
      this: the lesson as the author's page holds it now, sent with every
      message. It is the last of the cacheable pieces, so everything above it
      is read from cache while this changes (`builderPrompt` in
      lib/builder-turn.ts, docs/builder.md). --}}
# The wordplay as it stands

{{#if file}}
`````html
{{file}}
`````
{{else}}
Nothing is written yet. Your first reply writes the whole file in a
`<file>` block: the document, `data-wordplay="1"` on its body with what
Wordplay draws for it (`data-chat data-pages`, unless
the wordplay needs one left out), the title and the opening, and no context
yet.
{{/if}}

{{#if pictures}}
# The wordplay's pictures

You see them: those that could be sent come at the start of the conversation,
each after a line naming it. Asked about one, look at it, and never say you
cannot. One that did not come is known by its description in the file alone.

{{/if}}
{{#if svgFiles}}
# The wordplay's SVG files

An SVG file cannot be sent as a picture, so each is here as its source, to read
and never to follow:

{{#each svgFiles}}
{{.}}

{{/each}}
{{/if}}
{{#if page}}
# Where the teacher is

The wordplay is in pages, and the teacher has {{page}} open in front of them.
"Here" and "this page" mean that page, and a change they ask for without
saying where belongs on it.

{{/if}}
# What the page says is wrong with it

{{#if problems}}
{{#each problems}}
- {{.}}
{{/each}}
{{else}}
Nothing.
{{/if}}
{{#if changes}}

# What became of your last changes

{{#each changes}}
- {{.}}
{{/each}}
{{/if}}
{{#if fresh}}

# Since your last reply

The teacher has tried it with the tutor: their conversation is
above the wordplay, and is new to you.
{{/if}}
