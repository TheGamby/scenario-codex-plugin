# Scenario Codex Plugin - Implementation Status

Last updated: 2026-05-24
Release status: V0.1.1 complete

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
- Phase 5 usability improvements completed:
  - README includes concrete local setup, Marketplace, dry-run, generation, and
    troubleshooting notes.
  - Skill guidance uses live tool names such as `list_teams`,
    `get_model_schema`, `run_model`, `display_asset`, and `manage_jobs`.
  - Example prompts for common game-art workflows are documented.
- Phase 6 closed by decision:
  - OAuth works in Codex and remains the default.
  - API-key support was not implemented because no non-interactive requirement
    exists yet.
- Phase 7 release preparation completed:
  - Version is `0.1.1`.
  - Release tag `v0.1.1` is the intended stable V0.1.1 tag.
  - Maintenance follow-ups are tracked in `planing/follow-up-issues.md`.
  - Direct GitHub issue creation was attempted but the connector returned
    `403 Resource not accessible by integration`.
- Initial local commit exists on `main`.
- Release tags exist locally and on `origin`.

## Deliberately Not Done Automatically

- Personal Codex marketplace modification was made only after explicit user
  approval.
- No Scenario upload, training, or workflow execution was started.
- No API-key wrapper was added; OAuth remains the V1 authentication mode.
- Scenario account-specific team/project IDs are not documented in public-facing
  files.

## Requires Explicit User Action Or Approval

- None for V1 acceptance.

## Follow-Up Tracking

- GitHub issue creation should be retried manually or with a GitHub token that
  can create issues in `TheGamby/scenario-codex-plugin`.
- Until then, follow-up items are documented in
  `planing/follow-up-issues.md`.

## Current GitHub State

- Remote: `https://github.com/TheGamby/scenario-codex-plugin.git`
- `origin/main` points at the final documented V1 state.
- Tag `v0.1.1` points at the final V0.1.1 state on `origin`.
