# Scenario Codex Plugin - Implementation Status

Last updated: 2026-05-24
Release status: V0.2.0 complete, committed, tagged, and pushed

## Completed Locally

- Plugin scaffold exists in this repository:
  - `.codex-plugin/plugin.json`
  - `.mcp.json`
  - `.codex/config.toml`
  - `skills/scenario/SKILL.md`
  - `README.md`
- Version updated to `0.2.0`.
- V0.2 plan added at `planing/phase-plan-v0.2.md`.
- Live Scenario MCP inventory added at
  `planing/scenario-mcp-tool-inventory-v0.2.md`.
- Tool risk and budget matrix added at
  `planing/scenario-mcp-tool-matrix-v0.2.md`.
- README now documents:
  - installation from other local Codex projects
  - all 21 live Scenario MCP tools by area
  - safety and approval classes
  - budget preview, approval, and reporting rules
  - upload and asset workflows
  - analyze workflows
  - training workflows
  - collection, model, workflow, and job management
  - app-only tool limits
- Skill guidance now covers:
  - context-read, discovery-read, cost-preview, paid-generate, mutating-safe,
    destructive, training, and app-only classes
  - dry-run-first behavior for paid actions
  - exact confirmation for destructive actions
  - usage reporting and per-job billing checks
  - upload, analyze, training, collection, model, workflow, and job behavior
- JSON syntax checks passed for `.codex-plugin/plugin.json` and `.mcp.json`.
- TOML syntax check passed for `.codex/config.toml`.
- Plugin validator reported `Plugin validation passed`.
- `node`, `npm`, and `npx` are available in the Codex desktop context.
- `mcp-remote` connected to `https://mcp.scenario.com/mcp`.
- Scenario MCP reported server name `scenario-mcp` and version `0.9.0` during
  validation.
- Live `tools/list` discovery returned 21 tools:
  - `list_teams`
  - `list_projects`
  - `search`
  - `recommend`
  - `recommend_training`
  - `plan_generation`
  - `get_model_schema`
  - `run_model`
  - `train`
  - `manage_workflows`
  - `manage_jobs`
  - `display_asset`
  - `analyze`
  - `upload_asset`
  - `complete_upload`
  - `manage_assets`
  - `manage_models`
  - `manage_collections`
  - `usage`
  - `capture_3d_view`
  - `proxy_asset_fetch`
- V0.2 read-only and budget validation completed:
  - `list_teams` returned authenticated account data.
  - `search` returned public model data for a game-art query.
  - `get_model_schema` returned the live `GPT Image 2` schema.
  - `run_model` with `dry_run=true` returned `creativeUnitsCost: 2`,
    `creativeUnitsDiscount: 0`, and no job.
  - `usage` for a one-day range returned a compact totals report.
  - `manage_jobs check` returned billing for the existing validation job.
  - `analyze` with `action="caption"` and `dry_run=true` returned a zero-CU
    estimate for the existing validation asset.
  - `recommend_training` returned an image LoRA recommendation for
    `dataset_shape="single_images"`.
- Personal marketplace setup remains external to the repo:
  - `~/.agents/plugins/marketplace.json`
  - `~/plugins/scenario-codex-plugin` symlinked to this repository.

## Deliberately Not Done Automatically

- No Scenario upload was started.
- No Scenario training was started.
- No workflow was created, updated, run, approved, rejected, or deleted.
- No asset/model/collection destructive action was performed.
- No API-key wrapper was added; OAuth remains the supported authentication
  mode.
- Scenario account-specific team/project IDs are not documented in
  public-facing files.
- Personal marketplace files are not committed.

## Requires Explicit User Action Or Approval

- Real paid Scenario generation, analysis, workflow run, or training start.
- Uploading a user-selected local file.
- Destructive actions such as delete, cancel, transfer, or membership removal.
- GitHub issue creation, unless repository integration permissions are updated.

## Follow-Up Tracking

- GitHub issue creation should be retried manually or with a GitHub token that
  can create issues in `TheGamby/scenario-codex-plugin`.
- Until then, follow-up items are documented in
  `planing/follow-up-issues.md`.

## Current GitHub State

- Remote: `https://github.com/TheGamby/scenario-codex-plugin.git`
- `origin/main` points at the final V0.2.0 state.
- Tag `v0.1.1` points at the final V0.1.1 state on `origin`.
- Tag `v0.2.0` points at the final V0.2.0 state on `origin`.
