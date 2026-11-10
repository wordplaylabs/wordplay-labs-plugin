{{!--
THE .WPLAY FORMAT: THE ONE PLACE IT IS DEFINED

Labs serves this file at /format and, for an agent, at /format.md
(apps/wordplay/src/lib/format-reference.ts); the builder's guide includes it
whole (the spec binding in packages/player/prompts/wordplay-guide.md). The
parser in packages/format/src implements it, and spec.test.ts fails when
they drift: every ```html block below must parse with no problems, the
first is the fixture word for word, and each attribute table must equal the
parser's set. Edit the prose here; the numbers in double braces are bound
from packages/format/src/limits.ts. This file states the format and nothing
else: the decisions behind it and its history are docs/wordplay-format.md.

Licensed under Apache-2.0 with the rest of packages/ (packages/LICENSE).
--}}
# The `.wplay` format

Version 1.

## Scope

A wordplay is one HTML file that a reader works through in conversation
with a tutor: a language model that holds the teacher's notes and does
not give the answer away. The file holds what the reader sees, the
moment each part of it appears, and the notes only the tutor reads. This
document defines the file and what a host that runs it must do. It is in
two parts: the standard, which every host follows, and what Wordplay's
own site adds, which another host may ignore.

## Words

| Word | Means |
|---|---|
| wordplay | One `.wplay` file |
| reader | The person working through it |
| teacher | The person who wrote it |
| tutor | What the reader talks to |
| host | What runs the file: Wordplay's site, or another |
| conversation | One reader's run through a wordplay, with the tutor |
| turn | One message from the reader and the tutor's reply to it |
| page | The document as the reader sees it; with `data-pages`, one of its sections at a time |
| context | The teacher's notes for the tutor (`data-context`) |
| condition | The moment at which an element appears, in prose (`data-expect`) |
| reveal | Any element the reader sees that carries a condition |
| given | Shown to the reader |
| waiting | Not given, with its parent given: its condition is being judged |
| chat | Where the reader talks to the tutor, drawn by the host |
| accessible name | What a screen reader calls an element: its `aria-label`, its `<label>`, or its text |
| live region | An element marked `aria-live`, whose changes a screen reader announces |

## Conformance

The file and the host are the two things this document places
requirements on. A requirement says *must*; a permission says *may*; the
rest describes. Examples, and the tree drawn under one, are not
requirements.

## The file

A `.wplay` file is an HTML document. Four attributes make it a wordplay:

| Attribute | Where | Value | Means |
|---|---|---|---|
| `data-wordplay` | `<body>` | `"1"`, the version | This document is a wordplay. |
| `data-context` | A `<div>` directly in the `<body>` | None | The teacher's notes for the tutor. The reader never sees it. |
| `data-expect` | Any element the reader sees, or a calculator's key | The condition, in prose | The element is given once the condition holds. |
| `data-tutor` | An `<output>` | None | The tutor writes it. |

Everything else is HTML, shown as HTML, written so that a screen reader
can read it: every control has an accessible name, and what changes while
the reader works is in a live region. The `<head>` holds what any
document's does. A relative URL resolves against the file's own address,
so a picture or a sound sits beside the file. A host must ignore an
attribute it does not know and show the element as HTML.

The reader talks to the tutor in a chat the host draws; where it draws
it, and how many, is the host's.

## Reveals

**One rule.** A host must give an element with a condition once its
condition holds, and not before. Until then it is not given, takes no
room, and nothing of it reaches the browser, with everything inside it,
but the stand-in Privacy allows. An element without a condition is given
with its parent. The `<body>` is always given. A
reveal given stays given for the rest of the conversation.

**Order.** A condition is judged once the element's parent has been given,
and not before. Conditions do not wait for one another otherwise: a reveal
the reader never reaches blocks nothing after it. Two reveals that must
come one after the other nest, or the later one's condition says what it
follows.

**The condition.** `data-expect` holds the moment in prose, as the teacher
would recognize it in the conversation. It holds when the tutor judges
that it has, or when a script says so (`met`, under Scripts). The tutor
judges conditions at each turn, from the conversation and the page as it
reads them then; a change on the page alone starts no turn, so a page that
must give a reveal on its own calls `met`.

**Ids.** A reveal with no `id` is numbered in document order, `r1`, `r2`,
and so on; one with its own `id` goes by that id alone. A reveal with its
own `id` still uses up its number, unless it is a `<section>`: in the
example, `stuck` is the first reveal, so the next is `r2`. Context is
numbered in document order, `c1`, `c2`, and so on, and one with its own
`id` goes by that id alone, still using up its number. The tutor and
scripts name a reveal by its id.

**No steps.** A reveal is given and that is all: a wordplay has no steps,
no progress and no end.

## Notes

A context element holds the teacher's notes for the tutor, in prose: what
the reader is taken to know, the facts, the answers, and for each moment
the teacher prepared for, what to accept and what to say, the moment
written in front of its note ("When they ask why 4 is the bad number:
..."). A note may tell the tutor what to write on the page and when.

Context directly in the `<body>` is about the whole wordplay. The tutor
reads it from the start, with inline markup kept, so math and code
survive.

Context anywhere else, on another element, or with a condition of its own,
is a mistake a host may report; the host still strips it and still gives
the tutor its text, the condition written in front of it as the moment it
was for.

## The tutor

**Reading the page.** The tutor is given the page as a screen reader
reads it: the accessibility tree (role, name, state) of what is given, and
what the page's live regions have announced. Nothing about how the page is
drawn reaches it.

**Acting on the page.** Anything a reader could do with a keyboard, the
tutor can do by accessible name: press a button, set a field, choose an
option.

**Writing on the page.** The tutor writes only in an `<output>` marked
`data-tutor`, by its accessible name, when its notes tell it to. What it
writes becomes the element's text, plain, replacing what was there, at
most {{maxWritten}} characters. How it is drawn is the teacher's.

**What the tutor reads at each turn.** The tree of what is given and its
announcements; what the page told it since its last turn (`tell`); what
the reader worked out on a calculator, where the page has one
(`data-calculator`, under What Wordplay adds); the context; and each
waiting reveal and calculator key, by id and condition. Where a reader is
in the wordplay is never in the file: it follows from the conversation.
With no conversation, where there is no tutor to give anything, a host
gives every reveal.

## Privacy

A host must strip every element marked `data-context` before anything
reaches the reader's browser. A reveal must reach the browser only once
it is given, and without its `data-expect`; before that, nothing of it,
or at most a stand-in that keeps its room and holds none of its words
("Containers", under What Wordplay adds).

## Scripts

A script in the file runs in the reader's browser as on any page. Before
the body is read, the host puts one frozen object on the document,
`document.wordplay`:

| Member | Does | At most |
|---|---|---|
| `met(id)` | Says that reveal's condition holds: the host gives it. An id not waiting is ignored. A reveal `met` gives is the browser's report, kept apart from what the tutor gave. | |
| `tell(text)` | Gives the tutor the text at its next turn. A live region is read to the reader too; this is not. | {{maxAnnouncement}} characters |
| `say(text, chat?)` | Sends a message as the reader, in the host's chat; a host with more than one takes `chat`, a chat's accessible name. The tutor answers now and is told the page sent it. Returns a promise of the reply's text, once it ends; refused, it rejects with an error saying why. | {{maxSaid}} characters, {{maxPageSays}} a minute |
| `keep(value)` | Keeps one JSON value, the page's own state, with the conversation; each call replaces the last. A larger value is dropped, with a warning in the console. | {{maxKept}} bytes of its JSON, as UTF-8 |
| `kept` | The value last kept in this conversation, or `undefined`. There before the first script runs. | |

**`wordplay:met`** fires on a reveal as it is given, bubbling, with its id
in `event.detail.id`, and again for each reveal already given when the
page is drawn, in document order.

**The page's state is the conversation's.** The host keeps which reveals
have been given, each control's value, typed, checked or chosen, by its
`id`, else its `name`, else its accessible name, and, with `data-pages`,
which page is open. Drawing the page again, it sets them back and fires
`input` and `change` on each. What lives only in a script's variables is
kept with `keep` and read back from `kept`:

```js
// A pile drawn in <output id="pile">, taken from by <button id="take">.
const game = document.wordplay?.kept ?? { pile: 21 };
const draw = () => (document.querySelector("#pile").textContent = game.pile);
document.querySelector("#take").onclick = () => {
  game.pile -= 1;
  document.wordplay?.keep(game);
  draw();
  if (game.pile === 0) document.wordplay?.met("emptied");
};
draw();
```

The kept value belongs to one conversation: starting over starts from the
file. The tutor never reads it; it reads the page as drawn.

Opened as plain HTML, a page has no `document.wordplay`, so a script
writes `document.wordplay?.met("solved")`. A host may add members of its
own, which a script tests for: `document.wordplay?.share?.(…)`. A script
is readable by the reader: a secret goes in `data-context` or behind a
`data-expect`, never in a script.

## Example

Page 2 of *The game from Marienbad*:

```html
<!doctype html>
<html lang="en">
<body data-wordplay="1" data-chat>

<div data-context>
  <p>The game is Nim with one row. The computer plays perfectly.</p>
  <p>When they say the trick is to always take 1: it beats a careless player
  and loses to the computer. Ask what happens from 4.</p>
  <p>When they ask why 4 is the bad number: from 4, any move leaves 1, 2 or 3,
  and the opponent takes the rest.</p>
  <p>When they ask whether it works when the last stick loses: everything
  shifts by one, 1, 5, 9, 13, 17, 21.</p>
</div>

<section aria-label="One row, small steps">
  <p>There is only one row of 21 matches. On your turn you take 1, 2 or 3. The
  player who takes the last match wins. You go first. Play a few games, look
  for a pattern, and tell your tutor your idea.</p>

  <div class="matches" role="group" aria-label="21 matches. Take 1, 2 or 3.">…</div>
  <p aria-live="polite">Your move.</p>
  <button>Start again</button>

  <aside id="stuck" data-expect="they have lost three games in a row">
    <p>Try a smaller game first: who wins with 4 matches on your turn?</p>
  </aside>
  <script>
    // The game calls gameOver at the end of each game.
    let lost = 0;
    function gameOver(won) {
      lost = won ? 0 : lost + 1;
      if (lost === 3) document.wordplay?.met("stuck");
      if (won) document.wordplay?.tell("They beat the computer from 21 matches.");
    }
  </script>

  <section aria-label="One row solved"
    data-expect="they say, in their own words, always leave a multiple of 4">
    <p>You found the winning strategy.</p>

    <p><strong>Exercise.</strong> On <em>Fort Boyard</em>, candidates play this
    game against the Master of Time. 21 sticks, take 1, 2 or 3, the candidate
    goes first, and the player who takes the last stick <strong>loses</strong>.
    What is the strategy now? Can the candidate win at all?</p>

    <div class="matches" role="group" aria-label="21 sticks. Take 1, 2 or 3. The last stick loses.">…</div>
    <p aria-live="polite">Your move.</p>

    <section aria-label="Fort Boyard solved"
      data-expect="they say the candidate cannot win from 21">
      <p>Right: 21 is one more than a multiple of 4, so the Master of Time
      always wins.</p>
    </section>
  </section>
</section>

</body>
</html>
```

The matches and the script that plays the game are left out (`…`); the
file's own script calls `gameOver` at the end of each game. Opened as
plain HTML, with no host, a browser shows the whole file. Its parts, with
the conditions on the edges:

```
context c1 "The game is Nim with one row. The computer plays perfectl…"
reveal stuck  ── expect: they have lost three games in a row
reveal r2 "One row solved"  ── expect: they say, in their own words, always leave a multiple of 4
└── reveal r3 "Fort Boyard solved"  ── expect: they say the candidate cannot win from 21
```

## What Wordplay adds

The above is the standard. Wordplay's site adds what follows; another host
may ignore all of it, and the file is then its HTML: one page, math as its
source, `data-key` elements shown as their text, and no stand-in keeping a
container's room. Nothing of it is drawn unless the file asks: a wordplay
with none of it is its HTML, in the site's reading column.

**Declarations.** What is drawn around the page is declared on the
`<body>`, each a boolean attribute with no value:

- `data-chat`: the tutor's chat beside the page.
- `data-pages`: each `<section>` directly in the body without a condition
  is a page, one shown at a time, with the pages along the foot. A
  section with a condition is a reveal, drawn where it is.

**Links.** With `data-pages`, a link to an id on another page turns to that
page. A link to another wordplay on the site, by its name
(`<a href="sodium-2">`), opens it in place of this one, with the way back
over its title. A link off the site opens in a new tab. A link to a chat in
the page, or to anything in it, opens it where it is, and `href="#chat"`
opens the chat beside the page.

**Chats in the page.** A `<div data-chat>` is a chat with the tutor drawn
in it, after what it holds, named by its `aria-label`, else its first
heading. Every chat of a wordplay, in the page or beside it, belongs to one
conversation: the tutor reads every message in order, knowing which chat
it was said in, and answers in that chat, and the chat beside the page,
where there is one, shows it as a thread. Context directly in a chat is
about that chat, and the tutor reads it from the start. `say(text, chat)`
says the message in the chat named `chat`; with none, or a name no chat
has, in the chat beside the page; with no chat beside the page, the tutor
reads it and answers, and the reader sees neither. A chat holding an
`<output data-tutor>` is drawn by the teacher: Wordplay draws no box in it.

**Math.** `$…$` in text is a formula and `$$…$$` one on its own line, drawn
with KaTeX, except inside `code`, `pre`, `script`, `style` and fields. The
tutor reads the source as written.

**Given reveals.** A reveal not given is not in the page; one given
carries `data-met` set to its id (`data-met="solved"`), so the teacher's CSS can act on either
(`body:has(#solved) .hint { display: none }`), and fires `wordplay:met`
(Scripts).

Its attributes:

| Attribute | Where | Means |
|---|---|---|
| `data-chat` | The `<body>` | The tutor's chat beside the page. |
| | A `<div>` | A chat with the tutor drawn in it ("Chats in the page"). |
| `data-pages` | The `<body>` | The sections directly in it are pages, one shown at a time. |
| `data-calculator` | An `<input>` | The box has a calculator in its place; the box's value is what it works out (`86.4 s`). Its accessible name is its label. Its keys are the `data-key` elements in its `<label>`. The tutor reads what it works out. |
| | A chat | A calculator under the chat, `=` adding to the message. Its keys are the `data-key` elements in the chat. |
| `data-key="…"` | A `<data>` | A key on that calculator, never shown in the page: `number` (its text: `<data data-key="number">24 h</data>`), `constant` (its text the name, its `value` the value: `<data data-key="constant" value="9.81 m/s^2">g</data>`), `unit`, or `function` (a key from the calculator's library, by name). A key with a condition is put on the calculator once it holds, named `k1`, `k2`, and so on when it has no `id`, by its place among all the keys. |
| `data-container="…"` | A `<div>` | How its reveals appear: `stack`, one under another in the order they are given, taking no room before; `reserved`, the same, with room kept for all of them from the start; `fixed`, each in its own place, room kept for all of them. |
| `data-group` | A `<div>` | Elements the creator's editor keeps together. Nothing to the reader. |
| `data-person="…"` | Any element | Filled with the profile of the reader who wrote an entry on the wall: `mine`, or an entry's place in `entries`, from 0 ("Sharing"). |
| `data-opening="…"` | The `<body>`, or a chat | The tutor's first line in the chat beside the page, or in that chat: shown before anything is said, Markdown and math as in any reply, and the tutor is told it. Without it, the host's own line. At most {{maxOpening}} characters. |

**Containers.** In a `reserved` or `fixed` container, each reveal not yet
given has a stand-in: a hidden copy with its class, style and stylesheet,
every letter an `x`, nothing quoted, drawn or addressed. It says how much
room the reveal takes and nothing of what it says; a picture keeps only
its `height`. In a `stack` or `reserved` container, what has no condition
comes first, then the reveals in the order they were given.

**The name of a reveal**, in the chat and in the creator, is its
accessible name: its `aria-label`, else its first heading, else its id.

**The calculator's library**, the keys a `function` key may name:

{{keys}}

### Sharing

Every reader of a wordplay has one entry on its wall, which the page's
script writes, and every reader reads the whole wall:

| Member | Does | At most |
|---|---|---|
| `share(value)` | This reader's entry, any JSON, replacing their last; `null` takes it away. A value the shared block refuses is not sent, with a warning in the console. | {{maxShared}} bytes, {{maxShares}} a minute |
| `shared` | The wall, `{ mine, entries, count, tally }`: this reader's entry; every reader's, newest first ({{sharedEntries}} unless the block says); how many in all; and `tally`, for each field the block names, `{ [value]: n }` for a choice or boolean field and `{ n, sum }` for a number field, over every entry and not only those sent. An entry is `{ value, at, guest, by?, who?, facts? }`. There before the first script runs. | |
| `person(who)` | The site's chip for an entry's `who`, or the anonymous one for `null`. | |

**`wordplay:shared`** fires on the document when the wall changes. An
element marked `data-person` is filled with its entry's chip.

What the page shares is the reader's claim, since the page runs in their
browser. `by: "tutor"` (the tutor's judgment) and `facts` (the server's
count) are not: rank by those where fairness matters.

One optional `<script type="application/wordplay-shared">` bounds an
entry, as JSON, every key optional:

| Key | Means |
|---|---|
| `fields` | The entry is an object of these fields only: `{ "type": "number", "min", "max" }`, `{ "type": "choice", "of": [...] }`, `{ "type": "boolean" }` or `{ "type": "text", "max" }` (at most {{maxSharedText}}) |
| `names` | `true`: each entry carries `who`, its reader's name, handle and picture, `null` for a guest or a reader anonymous by choice |
| `guests` | `false`: a guest of a device may not share |
| `entries` | How many are sent, at most {{maxSharedEntries}} |
| `facts` | `"messages"`: how many messages the reader has sent |
| `tutor` | `true`: the tutor may write a reader's entry as it judges it |
| `tally` | A list of field names (`["guess"]`), each one of `fields`, to total over every entry: for a `choice` or `boolean` field, how many entries hold each value; for a `number` field, how many and their sum. A `text` field cannot be tallied |

The class's guesses, shown once the reader has guessed:

```html
<body data-wordplay="1" data-chat>
  <script type="application/wordplay-shared">
    { "fields": { "guess": { "type": "number", "min": 0, "max": 100 } } }
  </script>
  <label>Your guess <input id="guess" type="number"></label>
  <button id="send">Send</button>
  <p id="guessed" data-expect="they have guessed">The class: <output id="class"></output></p>
  <script>
    const draw = () => {
      const all = (document.wordplay?.shared?.entries ?? []).map((one) => one.value.guess);
      const out = document.querySelector("#class");
      if (out) out.textContent = all.join(", ");
    };
    document.querySelector("#send").onclick = () => {
      document.wordplay?.share?.({ guess: Number(document.querySelector("#guess").value) });
      document.wordplay?.met("guessed");
    };
    document.addEventListener("wordplay:shared", draw);
    document.addEventListener("wordplay:met", draw);
  </script>
</body>
```

### Standing

A wordplay reads where its reader stands on the wordplays it links to,
and on itself:

| Member | Does |
|---|---|
| `standing(name?)` | For a wordplay this file links to, by the name its link uses (`<a href="sodium-2">`): `{ title, readers, minutes, mine }`, its title, how many readers began it (`readers`, each once), its reading time in minutes, and `mine`, `true` once this reader has begun it, else `null`. With no name, this wordplay's own. A name the file does not link to answers `null`. There before the first script runs, and not changed while the page is open. |

A link to a wordplay carries `data-started` once a conversation of this
reader's began it, so a page colors its links in CSS alone. A guest of a
device has `mine` for this device's conversations. Another host answers
zero counts and `null` for every name.

### Checks

Wordplay reports, and still reads the file, what follows. The file: no
`data-wordplay` on the body; `data-wordplay` or `data-pages` on anything
but the body; a declaration with a value. Context: the body marked as
context; context anywhere but directly in the body or a chat, on anything
but a `<div>` (a choice's note in an answer apart), with a condition, or
holding a control. Conditions: a condition on the body; a condition empty
or over {{maxCondition}} characters; a `<section>` with a condition and no
accessible name; a condition in a wordplay with no chat anywhere. Chats:
unnamed, named as another, named for the box ("Chat about …"), or inside
context; a link to `#chat` with no chat beside the page; an element with
the id `chat`; an opening line on the body with no chat beside the page,
`data-opening` on anything but the body or a chat, or over {{maxOpening}}
characters. The tutor's outputs: `data-tutor` on anything but an
`<output>`; an `<output data-tutor>` unnamed, named as another, named with
a colon or inside context. Calculators and keys: `data-calculator` on
anything but an `<input>` or a chat; `data-key` on anything but a `<data>`,
or of an unknown kind; a key with no text, outside a calculator, or a
constant with no value; a calculator with more than {{maxKeys}} keys of
one kind. Containers: `data-container` on anything but a `<div>` or of an
unknown kind; `data-group` on anything but a `<div>`. Ids and attributes:
an id used twice by reveals, keys or context; an attribute within two
letters of one of these, or one no longer read; `document.tutor`, the
object's old name, in a script. And a shared block it cannot read.

Wordplay refuses to publish: no `data-wordplay`, or an unknown version; a
file over the limit; an id used twice by reveals, keys or context; a
shared block it cannot read; and a control with no accessible name where
no control is named and nothing is live, which the tutor could not see:
the whole page, or with `data-pages` each page alone and what is outside
them as one more.

## Limits

| What | At most |
|---|---|
| The file | {{maxFile}} characters |
| A condition | {{maxCondition}} characters |
| One live announcement, or one `tell` | {{maxAnnouncement}} characters |
| One line the tutor writes | {{maxWritten}} characters |
| One `say` | {{maxSaid}} characters |
| An opening line (`data-opening`) | {{maxOpening}} characters |
| What a page keeps (`keep`) | {{maxKept}} bytes |
| One entry shared (`share`) | {{maxShared}} bytes, {{maxShares}} a minute |
| A shared text field | {{maxSharedText}} characters |
| Entries sent | {{maxSharedEntries}} |
| Keys in one group of a calculator | {{maxKeys}} |

## Versions

`data-wordplay="1"` is the version this document defines. A file whose
version a host does not know is not a wordplay to it. A form the format
drops is no longer read. There is no custom element. The extension
is `.wplay`; editors highlight it as HTML.
