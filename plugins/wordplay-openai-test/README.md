# Wordplay

Write and publish wordplays from your own material. Save them in Wordplay's
creator, revise them together, and publish when you choose.

Install Wordplay in Codex or
[connect it in ChatGPT](https://test.wordplaylabs.com/create/ai?app=chatgpt), then say:
“Help me write a wordplay from my notes.”

A Wordplay account and plugin access are required. Sign in to Wordplay in
the browser and approve the connection. Your workspace may need to allow
custom MCP servers. Writing uses your AI app's available usage. Wordplay's
hosted tutor runs separately.

For Codex, open a checkout of the
[plugin distribution repository](https://github.com/wordplaylabs/wordplay-labs-plugin)
and install Wordplay from its repository marketplace. In Codex CLI:

```sh
codex plugin marketplace add wordplaylabs/wordplay-labs-plugin --ref main
codex plugin add wordplay@wordplay-openai
```

To try the test site, use `--ref test` for the marketplace and install
`wordplay-test@wordplay-openai-test`. The `test` branch contains Wordplay
(test site), which saves to the test site. The package
uses the [standard portable plugin format](https://developers.openai.com/plugins/build/plugins).
Distribution and public directory publication are separate; this package
does not claim an existing directory listing.

The connection can read your wordplays, including tutor notes, save changes
and publish under your name. Disconnect it in Settings, under Connected apps.
See [Privacy](https://test.wordplaylabs.com/privacy). Local preview and simulated students are
available through the separate Claude Code plugin.

Support: [plugin@wordplaylabs.com](mailto:plugin@wordplaylabs.com).
MIT licensed; see LICENSE.
