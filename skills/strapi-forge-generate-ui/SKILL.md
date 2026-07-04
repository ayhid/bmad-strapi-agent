---
name: strapi-forge-generate-ui
description: 'Generate Strapi v5 admin panel UI components using Strapi Design System - pages, components, routing, state management. Use when the user says "generate the plugin admin UI" or when invoked by the strapi-forge-create-plugin skill.'
---

# Generate UI

**Goal:** Generate the Strapi v5 admin panel side of a plugin using the Strapi Design System — plugin registration, pages, routing, reusable and custom field components, API client and data hooks, and translations. Typically invoked by the `strapi-forge-create-plugin` orchestrator with UI requirements and an architecture blueprint; when run standalone, gather those inputs from the user first.

## Conventions

- Bare paths (e.g. `instructions.md`) resolve from the skill root.
- `{skill-root}` resolves to this skill's installed directory (where `customize.toml` lives).
- `{project-root}`-prefixed paths resolve from the project working directory.
- `{skill-name}` resolves to the skill directory's basename.

## On Activation

### Step 1: Resolve the Workflow Block

Run: `python3 {project-root}/_bmad/scripts/resolve_customization.py --skill {skill-root} --key workflow`

**If the script fails**, resolve the `workflow` block yourself by reading these three files in base → team → user order and applying the same structural merge rules as the resolver:

1. `{skill-root}/customize.toml` — defaults
2. `{project-root}/_bmad/custom/{skill-name}.toml` — team overrides
3. `{project-root}/_bmad/custom/{skill-name}.user.toml` — personal overrides

Any missing file is skipped. Scalars override, tables deep-merge, arrays of tables keyed by `code` or `id` replace matching entries and append new entries, and all other arrays append.

### Step 2: Execute Prepend Steps

Execute each entry in `{workflow.activation_steps_prepend}` in order before proceeding.

### Step 3: Load Persistent Facts

Treat every entry in `{workflow.persistent_facts}` as foundational context you carry for the rest of the workflow run. Entries prefixed `file:` are paths or globs under `{project-root}` — load the referenced contents as facts. All other entries are facts verbatim.

### Step 4: Load Config

Load `{project-root}/_bmad/config.yaml` and `{project-root}/_bmad/config.user.yaml` (if present). Core keys (`user_name`, `communication_language`, `document_output_language`, `output_folder`) live at root or in the user file; module keys live under the `strapi-forge` section: `strapi_project_path`, `plugin_output_path`, `code_quality_level`. If neither file has a `strapi-forge` section, fall back to `{project-root}/_bmad/strapi-forge/config.yaml`. If no config exists at all, tell the user to run the `strapi-forge-setup` skill first, then continue with sensible defaults (`plugin_output_path` = `{project-root}/_bmad-output/strapi-plugins`, `code_quality_level` = standard).

### Step 5: Execute Append Steps

Execute each entry in `{workflow.activation_steps_append}` in order.

## Execution

Read fully and follow: `instructions.md`.
