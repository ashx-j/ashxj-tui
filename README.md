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

included `tsconfig.json` uses `moduleResolution: "Bundler"` + `skipLibCheck:
true`. Do not run `tsc --noEmit index.ts` (file argument); use `tsc --noEmit`
(the tsconfig form).

