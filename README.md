# Scenario Codex Plugin

Codex plugin for using Scenario from Codex through the official Scenario MCP
server.

The plugin is intentionally small:

- it registers the official Scenario MCP endpoint through `mcp-remote`
- it uses Scenario OAuth as the supported authentication flow
- it adds a Codex skill that guides discovery, budget previews, usage
  reporting, generation, asset handling, uploads, training, and workflow
  management
- it does not store Scenario API keys or add an API-key wrapper
- it keeps personal Codex marketplace entries outside the repository

Current release: `0.2.0`

## Requirements

- Codex with local plugin support
- Node.js and npm, so `npx` is available
- A Scenario account with access to the teams, projects, models, and assets you
  want to use

## What The Plugin Registers

The MCP server is configured in `.mcp.json`:

```json
{
  "mcpServers": {
    "scenario": {
      "command": "npx",
      "env": {
        "npm_config_cache": "/tmp/scenario-codex-plugin-npm-cache"
      },
      "args": [
        "-y",
        "mcp-remote@latest",
        "https://mcp.scenario.com/mcp"
      ]
    }
  }
}
```

The explicit `npm_config_cache` keeps `npx` away from user-level npm cache
permission problems in sandboxed Codex sessions. It does not contain Scenario
credentials.

The first Scenario operation that requires authentication should start the
Scenario OAuth flow. Complete that flow in the browser window opened by the MCP
client.

## Install For This Repo

Use the cloned repository directory as the plugin root. The plugin manifest is
at:

```text
.codex-plugin/plugin.json
```

1. Open this repository as the active Codex workspace.
2. Keep the workspace trusted in Codex.
3. Use the repo root as the local plugin root when adding a local plugin.
4. Verify that Codex reads `.codex-plugin/plugin.json`, `.mcp.json`, and
   `skills/` from this checkout.

For local MCP-only testing from this checkout, `.codex/config.toml` mirrors the
same Scenario MCP server configuration. This is useful for validating the MCP
connection without modifying personal marketplace files.

Implementation progress and release notes are tracked in `planing/`.

## Install For Other Local Projects

To use this plugin from other Codex chats or workspaces on the same machine,
make it available through your personal Codex marketplace. Keep the marketplace
file outside this repository; it is machine-local configuration.

1. Clone this repository:

```bash
git clone https://github.com/TheGamby/scenario-codex-plugin.git
```

2. Link the clone into the personal plugin directory:

```bash
mkdir -p "$HOME/plugins"
ln -s /absolute/path/to/scenario-codex-plugin "$HOME/plugins/scenario-codex-plugin"
```

If `~/plugins/scenario-codex-plugin` already exists, inspect it before replacing
it.

3. Create or update the personal marketplace file:

```text
~/.agents/plugins/marketplace.json
```

For a new personal marketplace, start from:

```text
examples/personal-marketplace.example.json
```

If you already have a marketplace file, merge only the
`scenario-codex-plugin` entry from the example into your existing `plugins`
array.

4. Restart Codex or reload the plugin marketplace, then install or enable the
Scenario plugin.

5. Start a new Codex chat and test:

```text
List my Scenario projects.
```

The first Scenario action that needs account data should trigger Scenario
OAuth. Complete that flow in the browser window opened by the MCP client.

Do not commit `~/.agents/plugins/marketplace.json`, OAuth tokens, npm caches, or
any Scenario API keys. This public repository intentionally contains no
Scenario secrets.

## Tool Coverage

V0.2 classifies all 21 live Scenario MCP tools returned by `tools/list`.

| Area | Tools |
| --- | --- |
| Context | `list_teams`, `list_projects`, `get_model_schema`, `display_asset`, `usage` |
| Discovery | `search`, `recommend`, `recommend_training`, `plan_generation` |
| Generation and analysis | `run_model`, `analyze` |
| Uploads | `upload_asset`, `complete_upload` |
| Jobs | `manage_jobs` |
| Asset/model/collection management | `manage_assets`, `manage_models`, `manage_collections` |
| Workflows | `manage_workflows` |
| App/viewer support | `capture_3d_view`, `proxy_asset_fetch` |

Full references:

- `planing/scenario-mcp-tool-inventory-v0.2.md`
- `planing/scenario-mcp-tool-matrix-v0.2.md`

## Safety And Approval Model

The plugin skill uses these behavior classes:

- `context-read`: safe reads such as team/project lookup, schema inspection,
  asset display, job checks, and usage summaries.
- `discovery-read`: search, recommendation, and generation planning without
  creating jobs or assets.
- `cost-preview`: dry-runs and usage reports used for budget planning.
- `paid-generate`: real model runs, analysis jobs, and workflow runs that can
  consume Creative Units.
- `mutating-safe`: uploads, metadata updates, tag changes, membership additions,
  and resource creation.
- `destructive`: deletes, job cancellation, transfers, removals, and workflow
  deletion.
- `training`: custom model training lifecycle actions.
- `app-only`: tools intended for MCP App/viewer contexts.

Read-only discovery can run without unnecessary prompts. Paid, mutating,
training, and destructive actions need explicit user intent. Destructive actions
need exact target confirmation.

## Budget Management

Before paid work, Codex should preview cost when the MCP supports it:

1. Confirm team/project context.
2. Inspect the model or workflow schema.
3. Summarize cost-impact parameters such as dimensions, output count,
   references, quality, duration, model, or workflow inputs.
4. Call a dry-run when available:
   - `run_model` with `dry_run=true`
   - `analyze` with `dry_run=true`
   - `train` with `dry_run=true` for `configure` or `start`
   - `manage_workflows run` with `dry_run=true` when supported
5. Report estimated Creative Units, expected outputs, and status strategy.
6. Ask for approval before the real run unless the user already explicitly
   requested that exact paid action.

Codex should not silently start several paid variants. For multi-variant work,
it should show the per-variant and total estimate first.

## Usage Reporting

Use two reporting paths:

- `manage_jobs check`: per-job status, progress, output asset IDs, and actual
  billing when a job ID exists.
- `usage`: date-range Creative Unit reporting with optional `include` sections
  such as `usages`, `usages.daily`, `modelUsages.daily`, `activity`, or
  `entities`.

Usage reports should include the date range, total Creative Units, important
models/jobs/assets when available, and a note when data may be delayed,
aggregated, or limited by permissions.

## Expected Workflow

When working with Scenario through Codex:

1. Discover teams and projects if the target context is unclear.
2. Use `list_teams` first, then `list_projects` when a team is known but the
   project is not.
3. Search or request model recommendations for open-ended generation requests.
4. Inspect the selected model schema with `get_model_schema` before running
   generation.
5. Use `run_model` with `dry_run=true` for cost/feasibility checks before a
   real generation when supported by the live MCP schema.
6. Run generation only when the requested action is clear and approved.
7. Display generated assets when possible, or report asset and job identifiers.
8. Use `manage_jobs` for long-running job status and actual billing.
9. Use `usage` for time-window budget reports.

Uploads, training, workflow execution, and destructive management operations
are explicit user actions, not side effects of discovery or planning.

## Upload And Asset Workflows

`upload_asset` starts an upload and `complete_upload` finalizes multipart
uploads. Codex should ask for the exact file and target project first, then:

1. Determine file name, content type, kind, and size.
2. Prefer multipart upload with `file_size`; reserve inline base64 for very
   small files when shell upload is unavailable.
3. PUT every multipart part to the presigned URLs exactly as instructed by MCP.
4. Call `complete_upload` after all parts succeeded.
5. Report the final asset ID and processing status.

Asset management rules:

- `manage_assets list/get/get_bulk/list_tags` are read actions.
- `update/copy/download/add_tags/remove_tags` are mutating actions.
- `delete` is destructive and needs exact `asset_ids` confirmation.
- Use `display_asset` for user-facing display instead of embedding CDN URLs.

## Analyze Workflows

`analyze` supports `caption`, `describe_style`, `detect`, `embed`,
`prompt_gen`, and `translate`.

Codex should ask for a Scenario `asset_id`, URL, or text input. Local files must
be uploaded first only after explicit approval. Use `dry_run=true` first when
analysis may cost Creative Units or create a job.

## Training Workflows

Training starts with `recommend_training`, then a dataset and budget plan.

Checklist:

- target project and model intent
- modality and dataset shape
- number and quality of examples
- target style/subject
- expected base family and model type
- dry-run estimate or explicit "no estimate available" note

`train` actions are `upload_images`, `update_image`, `delete_image`,
`set_pairs`, `configure`, `start`, and `stop`. No training action should be
started without explicit user approval, and `configure`/`start` should dry-run
when possible.

## Collections, Models, Workflows, And Jobs

- `manage_collections list/get` are read actions; `create/update/add_assets`
  and `add_models` are mutating; `remove_assets/remove_models/delete` require
  exact confirmation.
- `manage_models list/get/list_tags` are read actions; `create/update/copy`
  and tag changes are mutating; `transfer/delete` are high-risk.
- `manage_workflows list/get` are read actions; `create/update/approve/reject`
  are mutating; `run` may be paid and should dry-run first when possible;
  `delete` is destructive.
- `manage_jobs check/list` are reporting actions; `cancel` is destructive and
  needs exact `job_id` confirmation.

## App-Only Tools

`capture_3d_view` and `proxy_asset_fetch` are intended for MCP App or viewer
contexts. In normal Codex text chats, prefer `display_asset`, `manage_assets`,
or a user-provided asset ID instead of trying to fetch or capture viewer binary
data.

## Example Prompts

- `List my Scenario projects.`
- `Find a Scenario model for stylized fantasy game icons.`
- `Plan a Scenario image generation for a blue crystal inventory icon.`
- `Dry-run a small GPT Image 2 icon generation and report the estimated Creative Units.`
- `Show usage for this month and summarize the Creative Units.`
- `Check this Scenario job and report billing.`
- `Prepare a training plan for stylized item icons without starting training.`
- `Show the generated Scenario asset for this asset ID.`

## Minimal Dry-Run Workflow

This workflow checks cost and parameter shape without starting a generation:

1. Use `list_teams` and `list_projects` to confirm the target project.
2. Use `search`, `recommend`, or `plan_generation` to choose a model.
3. Call `get_model_schema` for the selected `model_id`.
4. Call `run_model` with `dry_run=true`, confirmed `team_id` and `project_id`,
   and parameters that match the schema.
5. Review the returned Creative Units before deciding whether to run the same
   request without `dry_run`.

The validated dry-run used `GPT Image 2` for a small stylized fantasy inventory
icon and estimated 2 Creative Units. A real 512x512 run failed in V0.1.1
validation because the live service requires at least 655,360 pixels for this
model, so the successful minimal generation used 1024x1024.

Successful V0.1.1 generation evidence:

- Model: `GPT Image 2`
- Parameters: `numOutputs=1`, `width=1024`, `height=1024`, `quality=low`,
  `background=opaque`
- Job: `job_shzQW61m5EoSkNZz6M8tdUmG`
- Asset: `asset_GQDDdE8VgMwqFfNQ7JVvATyk`
- Result display: `display_asset` returned the generated image

## Validated Setup

Validated on 2026-05-24 from this repository:

- `node --version`: `v25.9.0`
- `npx --version`: `11.12.1`
- `mcp-remote`: `0.1.37` as resolved by `npx -y mcp-remote@latest`
- Scenario MCP server: `scenario-mcp` `0.9.0`
- Live `tools/list`: 21 tools
- Plugin validator: `Plugin validation passed`
- JSON syntax checks: `.codex-plugin/plugin.json`, `.mcp.json`
- TOML syntax check: `.codex/config.toml`
- Read-only authenticated check: `list_teams` returned authenticated
  team/project data; exact account-specific names, IDs, and counts are omitted
  from this public README
- Read-only workflow check: `search` with `target="models"`, `public=true`,
  and query `game asset stylized` returned public models
- Schema check: `get_model_schema` returned the live `GPT Image 2` schema
- Budget dry-run check: `run_model` with `dry_run=true` returned
  `creativeUnitsCost: 2`, `creativeUnitsDiscount: 0`, and no job
- Budget reporting check: `usage` for a one-day range returned a compact totals
  report
- Job billing check: `manage_jobs check` returned the existing validation job
  as `success` with 2 CU billing
- Analyze budget check: `analyze` with `action="caption"` and `dry_run=true`
  returned a zero-CU estimate for the existing validation asset
- Training recommendation check: `recommend_training` returned an image LoRA
  recommendation for `dataset_shape="single_images"`

The default user npm cache on the test machine was not writable, so validation
used the same temporary npm cache configured in `.mcp.json`.

## Personal Marketplace Entry

This plugin can be loaded from the repository without copying its files by using
a personal marketplace entry and a symlink:

```text
~/.agents/plugins/marketplace.json
~/plugins/scenario-codex-plugin -> /absolute/path/to/scenario-codex-plugin
```

The marketplace entry points to `./plugins/scenario-codex-plugin` with
`policy.installation: "AVAILABLE"` and `policy.authentication: "ON_INSTALL"`.
This keeps the repository as the source of truth.

## API-Key Auth

API-key authentication is intentionally not part of V0.2. OAuth worked in Codex
during validation, so the optional API-key path remains out of scope. This
avoids storing secrets in plugin configuration and keeps OAuth as the supported
path. A future version may add a wrapper that reads `SCENARIO_API_KEY` and
`SCENARIO_API_SECRET` from the environment if OAuth becomes impractical for
non-interactive workflows.

## Troubleshooting

- `npx` not found: install Node.js/npm and reopen Codex.
- npm cache `EPERM`: keep the `npm_config_cache` value from `.mcp.json`, or
  repair ownership of your user-level npm cache outside this repository.
- OAuth does not start: retry the Scenario MCP action and check that pop-up or
  browser handoff is allowed.
- Authentication succeeds but writes fail: verify Scenario team/project
  permissions and whether the account can perform generation actions.
- Generation fails with a minimum-resolution error: inspect the model schema and
  job metadata, then raise `width` and `height`. `GPT Image 2` required at
  least 655,360 pixels during validation.
- Upload stalls: verify that all multipart PUT requests succeeded, then call
  `complete_upload`.
- Usage looks incomplete: retry later or request more `include` sections; usage
  data can be delayed or aggregated.
- Tool names or schemas differ from examples: Scenario MCP is beta, so inspect
  the live MCP tool descriptions and follow the current server contract.
