---
name: scenario
description: Use Scenario through the official Scenario MCP server for model discovery, asset generation planning, project context, budget previews, usage reporting, uploads, training, and game-art workflows from Codex.
---

# Scenario

Use this skill when the user asks about Scenario, Scenario MCP, Scenario models,
asset generation, game-art generation, usage, budget previews, asset upload,
model training, collections, workflows, or creative workflows that should run
through Scenario.

## Default Approach

- Use the official `scenario` MCP server exposed by this plugin.
- Treat OAuth as the supported authentication flow. Do not ask for or store
  Scenario API keys for this plugin.
- Start with read-only discovery when the user's team, project, model, asset,
  job, or workflow context is unclear.
- Prefer Scenario MCP tools over direct Scenario API calls.
- Do not commit account-specific team/project IDs, OAuth tokens, API keys,
  generated presigned URLs, or local machine paths.

## Safety Classes

- `context-read`: `list_teams`, `list_projects`, `get_model_schema`,
  `display_asset`, `usage`, `manage_jobs check/list`, and read variants of
  asset/model/collection/workflow tools. Use directly when needed.
- `discovery-read`: `search`, `recommend`, `recommend_training`, and
  `plan_generation`. Use directly for planning and model choice.
- `cost-preview`: `run_model dry_run`, `analyze dry_run`, `train dry_run`, and
  `usage`. Use before paid work when possible.
- `paid-generate`: real `run_model`, real `analyze` jobs, and workflow runs.
  Use a dry-run first when available and ask for approval unless the user has
  already explicitly requested the real paid action.
- `mutating-safe`: uploads, complete-upload, tag changes, metadata updates,
  collection membership additions, model copies, and workflow create/update.
  Confirm target team/project and intended change first.
- `destructive`: job cancellation, deletes, transfers, membership removals,
  workflow deletion, asset deletion, model deletion, and collection deletion.
  Require exact target IDs and explicit confirmation.
- `training`: `recommend_training` plus `train` actions. Never start training as
  a side effect of planning.
- `app-only`: `capture_3d_view` and `proxy_asset_fetch`. Use only in a suitable
  MCP App/viewer context.

## Core Workflow

1. Establish context.
   - If the team is unknown, call `list_teams` first.
   - If the team is known but the project is unknown, call `list_projects`.
   - If multiple teams or projects are returned, ask the user which one to use
     before continuing. Do not pick a project automatically.
   - If the user gives a project, model, asset, job, collection, or workflow
     name, resolve it to the Scenario identifier required by the MCP tool
     before taking action.

2. Choose the model deliberately.
   - For open-ended generation requests, use `recommend` for one-step
     generations, `plan_generation` for multi-step pipelines, or `search` when
     private/public model discovery by query is needed.
   - Before calling a model, call `get_model_schema` for the selected
     `model_id`.
   - Match user-facing prompt details to the model schema instead of guessing
     unsupported fields.
   - Highlight cost-impact fields such as output count, dimensions, references,
     duration, quality, or model family when the schema exposes them.

3. Preview budget before paid work.
   - For `run_model`, call `dry_run=true` whenever supported.
   - For `analyze`, call `dry_run=true` before non-trivial or job-backed
     analysis.
   - For `train configure` or `train start`, use `dry_run=true` when possible.
   - For `manage_workflows run`, use `dry_run=true` when the workflow accepts
     it.
   - If no dry-run is available, say that clearly and ask for explicit
     approval before starting.
   - Do not silently start several paid variants. Show per-variant and total
     estimated Creative Units first.

4. Ask for approval when required.
   - Include team/project context, tool/action, target model/workflow/job/asset,
     parameter summary, estimated Creative Units or "no estimate available",
     expected outputs, and polling/status plan.
   - If the user's latest message already explicitly authorizes the exact paid
     or mutating action, proceed without a duplicate confirmation.
   - For destructive actions, still require exact target confirmation unless
     the user has provided the exact target ID and action in the same request.

5. Execute and report.
   - After generation or analysis, show or display generated assets when the
     MCP tool supports it.
   - Use `manage_jobs check` for job status and actual billing when a job ID is
     available.
   - Use `usage` for time-range Creative Unit reporting, noting that usage data
     can be delayed or aggregated.
   - Report stable job IDs, asset IDs, model names, actual CU billing, and
     next steps. Do not paste oversized CDN URLs or binary data into chat.

## Budget And Usage Reporting

- Use `run_model dry_run`, `analyze dry_run`, `train dry_run`, or workflow
  dry-runs for estimates.
- Use `manage_jobs check` for per-job billing and status after a job exists.
- Use `usage` for date-range reporting. Default output is compact; request
  `include` sections such as `usages`, `usages.daily`, `modelUsages.daily`,
  `activity`, or `entities` only when the user needs the extra detail.
- A good report contains date range, total Creative Units, relevant models or
  jobs, asset-kind counts when present, and caveats about delayed aggregation.

## Uploads And Assets

- Use `upload_asset` only after the user explicitly selects the file and target
  project. Do not upload as a side effect of planning or analysis.
- For local files, prefer multipart upload with `file_size`; use inline base64
  only for very small files when shell upload is unavailable.
- Never upload secrets, private credentials, or unrelated local files.
- Finish multipart uploads with `complete_upload` only after all parts were
  uploaded successfully.
- Use `display_asset` for viewing assets and `manage_assets list/get` for
  metadata.
- Treat `manage_assets update/copy/download/add_tags/remove_tags` as mutating
  actions. Treat `delete` as destructive and require exact `asset_ids`.

## Analyze

- `analyze` actions are `caption`, `describe_style`, `detect`, `embed`,
  `prompt_gen`, and `translate`.
- Ask for a Scenario `asset_id`, URL, or text input instead of guessing a local
  file path. Upload local files first only after explicit approval.
- Use `dry_run=true` first when analysis may cost Creative Units or create a
  job.
- Report the analysis action, input asset/text summary, estimate, result, job
  ID when present, and any next recommended Scenario step.

## Training

- Start with `recommend_training` when the user asks about custom training.
- Confirm dataset shape, modality, intended style/subject, number and quality
  of examples, target project, and expected output model type.
- `train` actions are `upload_images`, `update_image`, `delete_image`,
  `set_pairs`, `configure`, `start`, and `stop`.
- Use `dry_run=true` for `configure` or `start` when possible.
- Do not start, stop, delete training images, or change pair/config state
  without explicit approval.

## Collections, Models, Workflows, And Jobs

- For `manage_collections`, `list` and `get` are read actions; `create`,
  `update`, `add_assets`, and `add_models` are mutating; `remove_assets`,
  `remove_models`, and `delete` require exact confirmation.
- For `manage_models`, `list`, `get`, and `list_tags` are read actions;
  `create`, `update`, `copy`, `download`, `add_tags`, and `remove_tags` are
  mutating; `transfer` and `delete` are high-risk actions.
- For `manage_workflows`, `list` and `get` are read actions; `create`,
  `update`, `approve`, and `reject` are mutating; `run` may be paid and should
  dry-run first when possible; `delete` is destructive.
- For `manage_jobs`, `check` and `list` are reporting actions; `cancel` is
  destructive and needs exact `job_id` confirmation.

## App-Only Tools

- `capture_3d_view` uploads a 3D viewer canvas capture as a new image asset.
  Use it only when an MCP App/viewer provides the PNG canvas data.
- `proxy_asset_fetch` fetches asset binary data for app rendering. Use
  `display_asset` or `manage_assets` instead in normal text chats.

## Troubleshooting

- If the Scenario MCP server is unavailable, first check that Node.js and `npx`
  are installed.
- If authentication fails, direct the user to complete the Scenario OAuth flow
  and verify their Scenario account permissions.
- If a generation or write action is denied, check Scenario project/team access
  and whether the account can perform generation actions.
- If a generation fails with a parameter error, inspect `get_model_schema` and
  the job metadata, then adjust dimensions, count, quality, references, or other
  model-specific parameters.
- Scenario MCP is beta; if a tool or schema differs from this guidance, inspect
  the live MCP tool descriptions and follow the current server contract.
