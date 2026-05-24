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
- Phase 4 real generation completed for project `Tartaros`:
  - First 512x512 run failed with job `job_eYGMeNVmtZ6nw9BdVuED3Bdz`; job
    metadata reported the model requires `width * height` of at least 655,360
    pixels.
  - A 1024x1024 dry-run still estimated `creativeUnitsCost: 2`.
  - A 1024x1024 real run succeeded with job
    `job_shzQW61m5EoSkNZz6M8tdUmG`.
  - The generated asset is `asset_GQDDdE8VgMwqFfNQ7JVvATyk`.
  - `display_asset` returned the generated image.
- Personal marketplace entry created:
  - `~/.agents/plugins/marketplace.json`
  - `~/plugins/scenario-codex-plugin` symlinked to this repository.
- Initial local commit exists on `main`.
- Local release tag `v0.1.0` exists.

## Deliberately Not Done Automatically

- Personal Codex marketplace modification was made only after explicit user
  approval.
- No Scenario upload, training, or workflow execution was started.
- No API-key wrapper was added; OAuth remains the V1 authentication mode.
- Scenario account-specific team/project IDs are not documented in public-facing
  files.

## Requires Explicit User Action Or Approval

- None for V1 acceptance.

## Current GitHub State

- Remote: `https://github.com/TheGamby/scenario-codex-plugin.git`
- `origin/main` exists.
- Tag `v0.1.0` exists on `origin`.
- Final generation and marketplace documentation is local until the next
  `main` and `v0.1.0` push.
