# Scenario MCP Tool Matrix - V0.2

This matrix maps every live Scenario MCP tool to Codex behavior. It is a
documentation and skill-guidance layer only; the plugin still delegates all
Scenario operations to the official Scenario MCP server.

## Risk Classes

| Class | Meaning | Default approval behavior |
| --- | --- | --- |
| `context-read` | Reads account, project, schema, job, asset, or usage context | No extra approval unless sensitive details would be exposed |
| `discovery-read` | Searches, recommends, or plans without creating assets or jobs | No extra approval |
| `cost-preview` | Estimates cost or reads usage/billing data | No extra approval, but report scope clearly |
| `paid-generate` | Can create paid jobs or output assets | Require dry-run when available, then explicit approval unless already requested |
| `mutating-safe` | Creates or updates resources without intended deletion | Require explicit approval with target context |
| `destructive` | Deletes, cancels, transfers, removes, or overwrites resources | Require explicit confirmation with exact target IDs |
| `training` | Manages training data, config, start/stop lifecycle | Require explicit approval; dry-run or cost note before start |
| `app-only` | Supports MCP App viewer flows rather than normal text chat | Use only when a suitable app/viewer context exists |

## Tool Classification

| Tool | Primary class | Dry-run or preview | Approval required before execution | Reporting requirement |
| --- | --- | --- | --- | --- |
| `list_teams` | `context-read` | none | no | Summarize choices; do not commit account IDs |
| `list_projects` | `context-read` | none | no | Summarize choices; ask if multiple projects match |
| `search` | `discovery-read` | none | no | Return matching model/asset names and stable IDs only when needed |
| `recommend` | `discovery-read` | accepts `max_cost_cu` as preference | no | Explain recommendation, constraints, and tradeoffs |
| `recommend_training` | `discovery-read` | none | no | Report base model family, dataset requirements, and alternatives |
| `plan_generation` | `discovery-read` | none | no | Produce a plan without starting generation |
| `get_model_schema` | `context-read` | schema inspection | no | Report supported parameters and cost-impact fields |
| `run_model` | `paid-generate` | `dry_run=true` | yes for real runs unless already explicit | Report estimate, job ID, status, CU billing, asset IDs |
| `train` | `training` | `dry_run=true` for `configure` or `start` | yes for all actions; stronger confirmation for start/stop/delete | Report dataset/config summary, estimate, status, IDs |
| `manage_workflows` | mixed: read, mutate, destructive, paid run | `dry_run=true` for `run` | yes for create/update/run/approve/reject/delete | Report action, workflow ID, job ID, status, changed fields |
| `manage_jobs` | mixed: read and destructive cancel | none | no for `check`/`list`; yes for `cancel` | Report job status, progress, CU billing, output IDs |
| `display_asset` | `context-read` | none | no | Display if host supports it; otherwise report asset ID/type |
| `analyze` | `cost-preview` or paid analysis | `dry_run=true` | yes for non-dry-run analysis that may run jobs | Report action, estimate, result, job ID when present |
| `upload_asset` | `mutating-safe` | local file-size preview | yes | Report file name, content type, kind, size, upload ID or asset ID |
| `complete_upload` | `mutating-safe` | upload part status | yes, tied to prior upload | Report upload ID, final asset ID, processing status |
| `manage_assets` | mixed: read, mutate, destructive | list/get before mutation | yes for update/copy/download/tags; exact confirmation for delete | Report before/after intent, asset IDs, tags/metadata changed |
| `manage_models` | mixed: read, mutate, destructive | list/get before mutation | yes for create/update/copy/transfer/download/tags; exact confirmation for delete/transfer | Report before/after intent, model IDs, target project when relevant |
| `manage_collections` | mixed: read, mutate, destructive | list/get before mutation | yes for create/update/membership changes; exact confirmation for delete/removal | Report collection ID and membership delta |
| `usage` | `cost-preview` | direct reporting | no | Report date range, total CU, per-model/job/asset summary if available |
| `capture_3d_view` | `app-only` and `mutating-safe` | none | yes in an app context | Report source 3D asset and new image asset ID |
| `proxy_asset_fetch` | `app-only` | none | no in an app context | Use for viewer rendering; avoid dumping binary data into chat |

## Action-Level Rules

### Jobs

- `manage_jobs check` and `manage_jobs list` are read/report actions.
- `manage_jobs cancel` is destructive and needs confirmation with the exact
  `job_id`.

### Workflows

- `manage_workflows list` and `get` are read actions.
- `create`, `update`, `approve`, and `reject` are mutating actions.
- `run` is a potentially paid action and should use `dry_run=true` first when
  the workflow supports it.
- `delete` is destructive and needs exact `workflow_id` confirmation.

### Assets

- `manage_assets list`, `get`, `get_bulk`, and `list_tags` are read actions.
- `update`, `copy`, `download`, `add_tags`, and `remove_tags` are mutating
  actions. `download` can expose files and must name the asset and format.
- `delete` is destructive and needs exact `asset_ids` confirmation.

### Models

- `manage_models list`, `get`, and `list_tags` are read actions.
- `create`, `update`, `copy`, `download`, `add_tags`, and `remove_tags` are
  mutating actions.
- `transfer` and `delete` are high-risk actions and need exact source and
  target confirmation.

### Collections

- `manage_collections list` and `get` are read actions.
- `create`, `update`, `add_assets`, and `add_models` are mutating actions.
- `remove_assets`, `remove_models`, and `delete` require exact confirmation
  because they remove membership or the collection itself.

### Training

- Start with `recommend_training` and a dataset checklist.
- `train configure` and `train start` should use `dry_run=true` when possible.
- `train start`, `stop`, `delete_image`, and pair/config changes require
  explicit user approval.

## Budget Approval Template

Before any real paid generation, workflow run, analysis job, or training start,
Codex should present:

- Team/project context: selected by the user or already unambiguous
- Tool and target: model, workflow, analysis action, or training model
- Parameters: compact summary of prompt, dimensions, output count, references,
  and cost-impact fields
- Estimate: Creative Units from dry-run, or a clear note that no estimate is
  available
- Expected output: job, asset, analysis result, training state, or workflow run
- Status plan: whether Codex waits, polls with `manage_jobs`, or returns a job
  ID

Codex should not silently start multiple paid variants. If the user asks for
several variants, report the per-variant estimate and total estimated CU before
starting real work.

## Reporting Template

After execution, report:

- Tool/action and target ID
- Dry-run estimate when one was used
- Actual job billing from `manage_jobs` when available
- `usage` period if a time-range report was requested
- Job status and progress
- Output asset IDs or displayed asset
- Any known caveats, such as delayed usage aggregation or missing detail due to
  permissions
