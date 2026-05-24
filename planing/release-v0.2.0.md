# Scenario Codex Plugin - Release V0.2.0

Release date: 2026-05-24

## Scope

V0.2.0 implements `planing/phase-plan-v0.2.md`. It expands the plugin from a
minimal OAuth-backed Scenario MCP setup into a more complete, safety-guided
Scenario workflow plugin with budget preview, approvals, and reporting rules.

## Included

- Version updated to `0.2.0`.
- Live Scenario MCP inventory for all 21 available tools.
- Action-level risk matrix for read-only, cost-preview, paid, mutating,
  destructive, training, and app-only behavior.
- Budget management guidance:
  - dry-run-first for paid actions where supported
  - approval template before real paid work
  - per-job and time-range reporting rules
- Usage reporting guidance through `usage` and `manage_jobs check`.
- Upload and asset workflow guidance for `upload_asset`, `complete_upload`,
  `display_asset`, and `manage_assets`.
- Analyze workflow guidance for `caption`, `describe_style`, `detect`, `embed`,
  `prompt_gen`, and `translate`.
- Training workflow guidance for `recommend_training` and `train`.
- Collection, model, workflow, and job management guidance.
- App-only guidance for `capture_3d_view` and `proxy_asset_fetch`.
- README and Scenario skill updated to the final V0.2 behavior.

## Validation Evidence

- Live `tools/list`: 21 Scenario MCP tools.
- Plugin validator: `Plugin validation passed`.
- JSON syntax checks passed for `.codex-plugin/plugin.json` and `.mcp.json`.
- TOML syntax check passed for `.codex/config.toml`.
- `list_teams`: authenticated read-only check succeeded.
- `search`: public model search succeeded.
- `get_model_schema`: `GPT Image 2` schema read successfully.
- `run_model dry_run`: returned `creativeUnitsCost: 2` and no job.
- `usage`: one-day compact usage report returned successfully.
- `manage_jobs check`: existing validation job reported 2 CU billing.
- `analyze dry_run`: caption dry-run on the existing validation asset returned
  a zero-CU estimate.
- `recommend_training`: image LoRA recommendation returned for
  `dataset_shape="single_images"`.

## Deliberately Not Included

- No custom Scenario REST API client.
- No stored Scenario API secrets.
- No committed personal marketplace files.
- No API-key wrapper while OAuth remains practical.
- No automated uploads, training starts, workflow runs, or destructive
  management actions.
- No account-specific team/project IDs in public-facing files.

## Follow-Up Planpoints

- Re-check Scenario MCP schemas after upstream changes.
- Retry GitHub issue creation when repository integration permissions allow it.
- Add real upload smoke tests only when a disposable public test asset and
  explicit approval are available.
- Add training dry-run smoke tests only when a disposable model target exists
  and explicit approval is available.
- Keep marketplace setup documented without committing machine-local
  marketplace files.
