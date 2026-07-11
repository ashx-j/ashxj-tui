# ashxj-tui

A custom slim chatbox extension for [pi](https://pi.dev). It
replaces the bulkier prompt box + statusline provided by
[`pi-zentui`](https://github.com/lmilojevicc/pi-zentui) with a low-profile,
rounded prompt box that auto-expands, a model-settings chip on the box's
border, and a slim stats line below.

## What it renders

- **Rounded prompt box** — a single low-profile element with rounded corners
  (`╭─╮` / `╰─╯`). As the typed message wraps to multiple rows the box grows
  taller (the sides extend) instead of staying one line and breaking.
- **Border chip** on the box's **bottom** border, right-aligned:
  `model · provider · effort` — e.g. `glm-5.2 · Ollama Cloud · xhigh`.
- **Slim stats line** directly **below** the box, one line, not wrapped around
  the input: `LSP · MCP · ✓ t/s · N tokens · context% · ↑in ↓out · $cost`.
- **No session-mode indicator** anywhere — pi has no such concept.

### Where each segment comes from

| Segment | Source |
|---|---|
| `model` (chip) | `ctx.model.id` |
| `provider` (chip) | derived from `ctx.model.provider` (`ollama-cloud` → `Ollama Cloud`) |
| `effort` (chip) | `pi.getThinkingLevel()` (`off`/`minimal`/`low`/`medium`/`high`/`xhigh`) |
| `LSP` | extension status `"lsp"` (published by `pi-lsp-extension`) |
| `MCP` | extension status `"mcp"` (published by `pi-mcp-adapter`) |
| `✓ t/s · N tokens` | extension status `"tps"` (published by `pi-tps-was-taken`) |
| `context%` | `ctx.getContextUsage()` + `ctx.model.contextWindow` |
| `↑in ↓out` | `ctx.sessionManager.getEntries()` assistant `usage.input/output` |
| `$cost` | assistant `usage.cost.total` |

The LSP/MCP/throughput values are not on `ctx` directly; they are read from
`footerData.getExtensionStatuses()` (the same mechanism zentui uses) and
rendered as-is, keeping their original styling.

## Install / disable zentui

This extension **replaces** the prompt box (`ctx.ui.setEditorComponent`) and the
statusline (`ctx.ui.setFooter`). zentui owns the same two surfaces, so the two
conflict (last-loaded wins). **Disable zentui and enable this one.**

```bash
# install as a local package (from the repo root)
pi install . -l
# then in ~/.pi/agent/settings.json: ensure pi-zentui is NOT in "packages",
# and ashxj-tui IS (or load it from an extensions path).
```

## Dev loop

```bash
# load ONLY this extension for fast iteration (from the repo root)
pi --no-extensions -e ./index.ts
```

Typecheck:

```bash
npm install
tsc --noEmit
```

### Note on the typecheck setup

`index.ts` imports `CustomEditor` as a **runtime value** from
`@earendil-works/pi-coding-agent` (we `extends` it to inherit pi's app
keybindings). All other types are declared inline (structural) so the file is
self-contained. Because importing `CustomEditor` pulls pi-coding-agent's `.d.ts`
(which re-exports files containing subpath imports like
`@earendil-works/pi-ai/compat` that don't resolve under plain `tsc`), the
included `tsconfig.json` uses `moduleResolution: "Bundler"` + `skipLibCheck:
true`. Do not run `tsc --noEmit index.ts` (file argument); use `tsc --noEmit`
(the tsconfig form).

## Credits

The extension replaces
[`pi-zentui`](https://github.com/lmilojevicc/pi-zentui), whose statusline
segments and editor-frame approach were the reference for the pi extension API.