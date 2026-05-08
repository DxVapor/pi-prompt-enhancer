# @dxvapor/pi-prompt-enhancer

A [pi coding agent](https://github.com/badlogic/pi-mono) extension that rewrites your prompts to be clearer, more specific, and more actionable.

Forked from [@danchamorro/pi-prompt-enhancer](https://github.com/danchamorro/pi-agent-toolkit/tree/main/packages/prompt-enhancer), extended with a **configurable dedicated model**, **side-by-side comparison popup**, **version history**, and **quick enhance mode**.

> **Migration:** This package was previously published as `pi-prompt-enhancer`. Install `@dxvapor/pi-prompt-enhancer` going forward.

---

## Features

### Two Enhancement Modes

| Mode | Shortcut | Description |
|------|----------|-------------|
| **Full Enhance** | `Ctrl+Shift+E` | Runs enhancement + shows comparison popup. Accept, reject, or chain another enhancement. Supports undo. History tracked automatically. |
| **Quick Enhance** | `Ctrl+Shift+Q` | In-place enhancement, no popup. Fast path when you already know you want it enhanced. |
| **Undo** | `Ctrl+Shift+Z` | Restores original prompt before enhancement |

### Comparison Popup

After `Ctrl+Shift+E`, a side-by-side overlay shows `ORIGINAL` and `ENHANCED` prompts:

- `Enter` / `A` — **Accept** enhanced prompt into editor
- `R` — **Reject** and restore the original
- `E` — **Enhance Again** — chain further enhancement on the result
- `Esc` — Cancel without changes

### Version History

Every enhancement is recorded in a version table (tag, summary, timestamp). Access it anytime:

- **Shortcut:** `Ctrl+Shift+H`
- **Command:** `/enhance-history`

History table shows: `Tag | Summary | Time`. Navigate with `↑↓`, `Enter` to load a previous version back into the editor. History persists across pi restarts via session entries.

### Configurable Dedicated Model

Pin a specific model for enhancement (e.g. a free GitHub Copilot model) regardless of what model is active in your coding session. Falls back to active session model when none is configured.

Change it quickly without leaving the keyboard: `Ctrl+Shift+M` opens the model picker and saves to project config. The current draft in the editor is preserved.

### Searchable Model Picker

`/enhance-model` opens a filterable overlay. Type to search, `↑↓` navigate, `Enter` select. No need to edit JSON by hand.

### Status Bar Indicator

Shows `✨ <model-id>` when a model is pinned, and briefly `✨ Enhancing (<model>)...` while running.

---

## Prerequisites

- [pi coding agent](https://github.com/badlogic/pi-mono) installed and running
- At least one AI provider configured in pi (run `/model` inside pi to verify)

---

## Installation

### Option A — `pi install` (recommended)

```bash
pi install npm:@dxvapor/pi-prompt-enhancer
```

### Option B — `extensions` entry in `settings.json`

Clone the repo anywhere on your machine:

```bash
git clone https://github.com/DxVapor/pi-prompt-enhancer.git ~/projects/pi-prompt-enhancer
```

Add to `~/.pi/agent/settings.json`:

```json
{
  "extensions": [
    "~/projects/pi-prompt-enhancer"
  ]
}
```

Then run `/reload` inside pi.

### Option C — copy into the extensions directory

```bash
mkdir -p ~/.pi/agent/extensions/prompt-enhancer
cp ~/projects/pi-prompt-enhancer/{index.ts,clean.ts} ~/.pi/agent/extensions/prompt-enhancer/
```

> **Note:** If you previously had `@danchamorro/pi-prompt-enhancer` or `pi-prompt-enhancer` in your `packages` list, remove it first to avoid duplicate command registration.

---

## Configuration

### Interactive (recommended)

Run `/enhance-model` inside pi. Two sequential prompts appear:

1. **Scope** — global (all projects) or project-local
2. **Model** — fuzzy-searchable list of every model with auth configured

There is a **"(use active session model)"** option at the top to clear a previously pinned model.

The command writes to the JSON config file automatically (creating directories as needed).

### Manual JSON

**Global** — applies to all projects:

```
~/.pi/agent/extensions/prompt-enhancer.json
```

**Project-local** — overrides global for one project:

```
<project-root>/.pi/extensions/prompt-enhancer.json
```

Format:

```json
{ "model": "gpt-5-mini" }
```

`model` must match the `id` of a model in pi's registry.

### Model resolution order

| Priority | Source |
|---|---|
| 1 | `model` in project config (`.pi/extensions/prompt-enhancer.json`) |
| 2 | `model` in global config (`~/.pi/agent/extensions/prompt-enhancer.json`) |
| 3 | Active session model — original upstream behaviour |

> **Tip:** When the same model ID exists under multiple providers (e.g. `gpt-5-mini` under both `azure-openai-responses` and `github-copilot`), the extension picks the first one that has auth configured.

---

## Usage

### Shortcuts

| Keybind | Action |
|---------|--------|
| `Ctrl+Shift+E` | Full enhance — popup with original vs enhanced comparison |
| `Ctrl+Shift+Q` | Quick enhance — in-place, no popup |
| `Ctrl+Shift+Z` | Undo — restore original prompt |
| `Ctrl+Shift+H` | Open version history table |
| `Ctrl+Shift+M` | Quick pick enhancement model (project scope) |

### Commands

| Command | Action |
|---------|--------|
| `/enhance <prompt>` | Enhance inline text, place result in editor |
| `/enhance-model` | Fuzzy-searchable model picker |
| `/enhance-history` | Browse prompt version history |

---

## Differences from upstream

| | [@danchamorro/pi-prompt-enhancer](https://github.com/danchamorro/pi-agent-toolkit/tree/main/packages/prompt-enhancer) | `@dxvapor/pi-prompt-enhancer` |
|---|---|---|
| Model used | Active session model | Config file → fallback to active model |
| Config | — | `prompt-enhancer.json` (global + project) |
| Model picker | — | Fuzzy-searchable `/enhance-model` |
| Comparison popup | — | Side-by-side original vs enhanced after `Ctrl+Shift+E` |
| Version history | — | Persistent version table via `Ctrl+Shift+H` |
| Quick enhance | — | `Ctrl+Shift+Q` for no-popup enhancement |
| Status bar | — | `✨ <model-id>` when pinned |
| Duplicate provider fix | — | Searches authed models first |

---

## Contributing

Contributions welcome. Open an issue before large PRs.

1. Fork the repo and create a branch: `git checkout -b feat/your-feature`
2. Make changes
3. Commit using [Conventional Commits](https://www.conventionalcommits.org/)
4. Open a pull request against `main`

For bug reports, include:
- pi version (`pi --version`)
- `prompt-enhancer.json` contents (redact keys)
- Observed error or behaviour

---

## License

MIT — see [LICENSE](./LICENSE).

Original extension by [Daniel Chamorro](https://github.com/danchamorro).
