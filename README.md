# pi-prompt-enhancer

A fork of [@danchamorro/pi-prompt-enhancer](https://github.com/danchamorro/pi-agent-toolkit/tree/main/packages/prompt-enhancer) for the [pi coding agent](https://github.com/badlogic/pi-mono), extending it with a **configurable dedicated model** for prompt enhancement.

Instead of always using whatever model is currently active in your session, you can pin a specific model (e.g. a free GitHub Copilot model) that is used exclusively for enhancement — regardless of what you have selected for regular coding work.

---

## Features

- `Ctrl+Shift+E` — enhance the current editor text in-place
- `Ctrl+Shift+Z` — restore original prompt (undo enhancement)
- `/enhance <prompt>` — enhance inline text, place result in editor
- `/enhance-model` — interactive picker to set the enhancement model from your configured providers
- Status bar indicator showing the currently pinned model (`✨ gpt-5-mini`)
- Global and project-level config, with project taking precedence
- Merge-safe config writes — other keys in the JSON are preserved

---

## Prerequisites

- [pi coding agent](https://github.com/badlogic/pi-mono) installed and running
- At least one AI provider configured in pi (run `/model` inside pi to verify)

---

## Installation

### Option A — `extensions` entry in `settings.json` (recommended)

Clone the repo somewhere on your machine:

```bash
git clone https://github.com/DxVapor/pi-prompt-enhancer.git ~/projects/pi-prompt-enhancer
```

Add it to `~/.pi/agent/settings.json`:

```json
{
  "extensions": [
    "~/projects/pi-prompt-enhancer"
  ]
}
```

Then run `/reload` inside pi.

### Option B — copy into the extensions directory

```bash
cp ~/projects/pi-prompt-enhancer/{index.ts,clean.ts} ~/.pi/agent/extensions/prompt-enhancer/
```

> **Note:** If you previously had `"npm:@danchamorro/pi-prompt-enhancer"` in your `packages` list, remove it first to avoid duplicate command registration.

---

## Configuration

### Interactive (recommended)

Run `/enhance-model` inside pi. Two sequential prompts appear:

1. **Scope** — global (all projects) or project-local
2. **Model** — every model that has auth configured in pi's registry

There is a **"↩ Use active session model"** option at the top to clear a previously pinned model.

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

Both files use the same format:

```json
{ "model": "gpt-5-mini" }
```

`model` must match the `id` of a model in pi's registry. Run `/model` inside pi to browse available IDs.

### Model resolution order

| Priority | Source |
|---|---|
| 1 | `model` in project config (`.pi/extensions/prompt-enhancer.json`) |
| 2 | `model` in global config (`~/.pi/agent/extensions/prompt-enhancer.json`) |
| 3 | Active session model — original upstream behaviour |

> **Tip:** When the same model ID exists under multiple providers (e.g. `gpt-5-mini` appears under both `azure-openai-responses` and `github-copilot`), the extension picks the first one that has auth configured — so you always get a working model rather than a silent failure.

---

## Usage

| Key / Command | Action |
|---|---|
| `Ctrl+Shift+E` | Enhance editor text in-place |
| `Ctrl+Shift+Z` | Restore original (undo) |
| `/enhance <prompt>` | Enhance inline text, place result in editor |
| `/enhance-model` | Pick or reset the dedicated enhancement model |

The status bar shows `✨ <model-id>` when a model is pinned. While enhancement is running it briefly shows `✨ Enhancing (<model-name>)...`.

---

## How it differs from the upstream

| | [@danchamorro/pi-prompt-enhancer](https://github.com/danchamorro/pi-agent-toolkit/tree/main/packages/prompt-enhancer) | This fork |
|---|---|---|
| Model used | Active session model | Config file → fallback to active model |
| Config | — | `prompt-enhancer.json` (global + project) |
| Model picker | — | `/enhance-model` command |
| Status bar | — | `✨ <model-id>` when pinned |
| Duplicate provider fix | — | Searches authed models first |

The system prompt, enhancement logic, `clean()`, and all shortcuts are unchanged from upstream.

---

## Contributing

Contributions are welcome. Please open an issue before submitting a large PR so we can discuss the approach first.

1. Fork the repo and create a branch: `git checkout -b feat/your-feature`
2. Make your changes
3. Commit using [Conventional Commits](https://www.conventionalcommits.org/): `feat(config): add support for per-session overrides`
4. Open a pull request against `main`

For bug reports, please include:
- pi version (`pi --version`)
- Contents of your `prompt-enhancer.json` (redact any keys)
- The error notification or behaviour you observed

---

## License

MIT — see [LICENSE](./LICENSE).

Original extension by [Daniel Chamorro](https://github.com/danchamorro).
