# Wordplay for Claude Code

Write a wordplay from your own material (a book, notes, a conversation) as
one `.wplay` file in your folder, try it with the tutor and simulated
students on your own Claude plan, and publish it on
[Wordplay](https://wordplaylabs.com) when you say so. A wordplay is a page a
reader works through in conversation with a tutor that has the answer and
will not give it.

## Install

```
claude plugin install wordplay --marketplace wordplaylabs/wordplay-labs-plugin
```

It needs Node 20 or later. Installed inside a session, `/reload-plugins`
loads it.

## Start

In the folder for your wordplay:

```
claude "/wordplay:create"
```

It answers that the creator is open in your browser at
`http://127.0.0.1:4747/preview/local`, on `untitled.wplay` in this folder.
It saves as you type; keep the terminal open while you work. No browser
opened (over SSH, in a container)? It gives the address to open yourself.

The full steps for each app, the Claude app and others included:
https://creator.wordplaylabs.com/ai

## What it adds

| Part | What it is | What it runs or reaches |
|---|---|---|
| `labs` server | Wordplay's MCP server: find and read wordplays, write yours in the creator, publish one when you ask | https://wordplaylabs.com; signs you in through your browser the first time; Claude Code asks you before each publish |
| `preview` server | Wordplay's creator on a wordplay in your folder, with its assistant and the tutor | Runs on your machine with Node, at `http://127.0.0.1:4747`; opens your browser once a session; runs the assistant and the tutor through your own Claude Code (`claude -p`); keeps each conversation under `.trials` beside the wordplay |
| `/wordplay:create` | Opens the creator on a wordplay in this folder, or a new one | The preview |
| `/wordplay:trial`, `learner` agent | Simulated students work through a wordplay, then a report of what a machine can check | The preview, on your plan |
| `builder` agent | Writes a wordplay with you in conversation, one choice at a time: `claude --agent wordplay:builder` | Your folder and the preview |
| `write-a-wordplay` skill | How to write a good one, picked up when you ask for a wordplay or a lesson from your material | Nothing |
| `player/` | The page, the tutor and the creator, built from Wordplay's own components as minified bundles for the preview | Files on your machine |
| `bridge/wordplay.mjs` | The preview server, as readable source | Started by Claude Code with Node |

## Keys and data

No API key, ever: the assistant and the tutor run on your own Claude Code
and your plan, and publishing signs you in through your browser. What
leaves your machine is a wordplay you publish, under your account, and the
prompts the assistant and the tutor send to your own Claude. Your files and
the trials beside them stay where they are; add `.trials/` to `.gitignore`
if you would rather not commit them.

## The test site's plugin

`wordplay-test` is the same plugin built for https://test.wordplaylabs.com,
from this repository's `test` branch. Try a change there before the site's
plugin has it:

```
claude plugin install wordplay-test --marketplace wordplaylabs/wordplay-labs-plugin#test
```

## Update

```
claude plugin update wordplay@wordplay
```

## License

Apache-2.0, as the packages it is built from. Questions and problems:
https://github.com/wordplaylabs/wordplay-labs/issues (the source).
