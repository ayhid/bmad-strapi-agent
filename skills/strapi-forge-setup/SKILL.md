---
name: 'strapi-forge-setup'
description: Sets up the Strapi Plugin Forge module in a project. Use when the user requests to 'install strapi-forge', 'configure Strapi Plugin Forge', or 'setup Strapi Plugin Forge'.
---

# Module Setup

## Overview

Installs, configures, and upgrades the Strapi Plugin Forge module in a project. Module identity (name, code, version) comes from `./assets/module.yaml`. Collects user preferences and writes them to three files:

- **`{project-root}/_bmad/config.yaml`** — shared project config: core settings at root (e.g. `output_folder`, `document_output_language`) plus a section per module with metadata and module-specific values. User-only keys (`user_name`, `communication_language`) are **never** written here.
- **`{project-root}/_bmad/config.user.yaml`** — personal settings intended to be gitignored: `user_name`, `communication_language`, and any module variable marked `user_setting: true` in `./assets/module.yaml`. These values live exclusively here.
- **`{project-root}/_bmad/module-help.csv`** — registers module capabilities for the help system.

Both config scripts use an anti-zombie pattern — existing entries for this module are removed before writing fresh ones, so stale values never persist.

`{project-root}` is a **literal token** in config _values_ (the data written into the files above) — never substitute it there. It signals to the consuming LLM that the value is relative to the project root, not the skill root. **This does not apply to the filesystem path _arguments_ passed to the scripts below** (the `--*-path`, `--*-dir`, and `--target` arguments): those are real paths, so you **must** resolve `{project-root}` to the actual project root before running, or the scripts will write to a literal `{project-root}/` directory under the skill folder. The scripts reject an unresolved token with an error.

## On Activation

1. Read `./assets/module.yaml` for module metadata and variable definitions (the `code` field is the module identifier: `strapi-forge`)
2. Check if `{project-root}/_bmad/config.yaml` exists — if a `strapi-forge` section is already present, inform the user this is an update and use its values as defaults
3. Check for per-module configuration at `{project-root}/_bmad/strapi-forge/config.yaml` and `{project-root}/_bmad/core/config.yaml`. If either file exists, inform the user that installer config was detected — its values will be consolidated into the shared config format and the per-module files cleaned up after setup.

If the user provides arguments (e.g. `accept all defaults`, or inline values like `my Strapi project is at ../my-app, strict review`), map any provided values to config keys, use defaults for the rest, and skip interactive prompting. Still display the full confirmation summary at the end.

## Collect Configuration

Ask the user for values. Show defaults in brackets. Present all values together so the user can respond once with only the values they want to change (e.g. "strict review, rest are fine"). Never tell the user to "press enter" or "leave blank" — in a chat interface they must type something to respond.

**Default priority** (highest wins): existing config values > legacy per-module config values > `./assets/module.yaml` defaults.

**Core config** (only if no core keys exist yet): `user_name` (default: the OS username), `communication_language` and `document_output_language` (default: English — ask as a single language question, both keys get the same answer), `output_folder` (default: `{project-root}/_bmad-output`). Of these, `user_name` and `communication_language` are written exclusively to `config.user.yaml`. The rest go to `config.yaml` at root and are shared across all modules.

**Module config**: Read each variable in `./assets/module.yaml` that has a `prompt` field and ask using that prompt with its default value (or legacy value if available):

- `strapi_project_path` — path to a Strapi v5 project used for testing generated plugins (may stay empty)
- `plugin_output_path` — where generated plugins are written
- `code_quality_level` — standard | strict | enterprise

## Write Files

Write a temp JSON file with the collected answers structured as `{"core": {...}, "module": {...}}` (omit `core` if it already exists). Values inside this JSON keep the literal `{project-root}` token. Then run both scripts — they can run in parallel since they write to different files.

In the commands below, replace `{project-root}` in every path argument with the actual project root before running — these are filesystem paths, not config values.

```bash
python3 ./scripts/merge-config.py --config-path "{project-root}/_bmad/config.yaml" --user-config-path "{project-root}/_bmad/config.user.yaml" --module-yaml ./assets/module.yaml --answers {temp-file} --legacy-dir "{project-root}/_bmad"
python3 ./scripts/merge-help-csv.py --target "{project-root}/_bmad/module-help.csv" --source ./assets/module-help.csv --legacy-dir "{project-root}/_bmad" --module-code strapi-forge
```

Both scripts output JSON to stdout with results. If either exits non-zero, surface the error and stop. If `python3` lacks PyYAML, run them with `uv run` instead (they declare PEP 723 dependencies). The scripts automatically read legacy config values as fallback defaults, then delete the legacy files after a successful merge. Check `legacy_configs_deleted` and `legacy_csvs_deleted` in the output to confirm cleanup.

Run `./scripts/merge-config.py --help` or `./scripts/merge-help-csv.py --help` for full usage.

## Create Output Directories

After writing config, create any output directories that were configured. For filesystem operations only, resolve the `{project-root}` token to the actual project root and create each path-type value from `config.yaml` that does not yet exist — this includes `output_folder` and `plugin_output_path`. The paths stored in the config files must continue to use the literal `{project-root}` token; only the directories on disk should use the resolved paths. Use `mkdir -p` or equivalent.

Do NOT create `strapi_project_path` — it points at the user's existing Strapi app; if it is set but does not exist, warn the user instead.

## Confirm

Show a summary table of everything written: core keys (marking which live in `config.user.yaml`), the `strapi-forge` section values, and the number of help entries registered. Then point the user at the module:

- Talk to **Nadir, the Plugin Architect** — `strapi-forge-agent-architect` — to plan and create a plugin end-to-end
- Or invoke a workflow directly: `strapi-forge-create-plugin` (full pipeline), `strapi-forge-quick-scaffold` (fast scaffold), `strapi-forge-plugin-audit` (audit an existing plugin)
