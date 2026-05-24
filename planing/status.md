# Scenario Codex Plugin - Implementation Status

Last updated: 2026-05-24

## Completed Locally

- Plugin scaffold exists in this repository:
  - `.codex-plugin/plugin.json`
  - `.mcp.json`
  - `.codex/config.toml`
  - `skills/scenario/SKILL.md`
  - `README.md`
- JSON syntax checks passed for `.codex-plugin/plugin.json` and `.mcp.json`.
- TOML syntax check passed for `.codex/config.toml`.
- Plugin validator reported `Plugin validation passed`.
- `node`, `npm`, and `npx` are available in the Codex desktop context.
- `mcp-remote` connected to `https://mcp.scenario.com/mcp`.
- Scenario MCP reported server name `scenario-mcp` and version `0.9.0`.
- OAuth-authenticated read-only `list_teams` returned real account data.
- Read-only model search returned public Scenario model data.
- Read-only generation planning returned a pipeline without starting a job.
- Live tool discovery confirmed:
  - `get_model_schema` is read-only and requires `model_id`.
  - `run_model` requires `model_id`, supports `dry_run`, and expects confirmed
    `team_id` and `project_id` for OAuth callers.
  - `display_asset` is read-only and requires `asset_id`.
  - `manage_jobs` supports job checks after a generation starts.
- Phase 4 dry-run completed for project `Tartaros`:
  - Specialized public LoRA model `Stylized Game Icons & Props` was discovered
    and its schema was read, but `run_model` dry-run returned a server-side
    `400` saying custom models only are supported for that endpoint.
  - `GPT Image 2` schema was read successfully.
  - `run_model` with `dry_run=true` returned `creativeUnitsCost: 2`,
    `creativeUnitsDiscount: 0`, and an empty job object.
  - No generation job was created and no asset was produced.
- Initial local commit exists on `main`.
- Local release tag `v0.1.0` exists.

## Deliberately Not Done Automatically

- No personal Codex marketplace file was modified.
- No Scenario upload, training, workflow execution, or generation job was
  started.
- No API-key wrapper was added; OAuth remains the V1 authentication mode.
- No Scenario account-specific team/project names or IDs are documented in
  public-facing files.

## Requires Explicit User Action Or Approval

- Push the dry-run documentation update to `origin/main` after review.
- Run a paid or credit-consuming Scenario generation from a user-selected team,
  project, and model if full Phase 4 generation validation is desired.
- Optionally create a personal marketplace entry if the user wants this plugin
  to appear in the Codex plugin marketplace UI.

## Current GitHub State

- Remote: `https://github.com/TheGamby/scenario-codex-plugin.git`
- `origin/main` exists.
- Tag `v0.1.0` exists on `origin`.
- Phase 4 dry-run documentation is local until the next `main` push.
