# Scenario MCP Tool Inventory - V0.2

Inventory date: 2026-05-24
MCP endpoint: `https://mcp.scenario.com/mcp`
Transport: `npx -y mcp-remote@latest`
Server reported earlier in validation: `scenario-mcp` `0.9.0`

This inventory records the live Scenario MCP tools visible to Codex for the
V0.2 documentation pass. It intentionally omits account-specific team and
project identifiers.

## Summary

- Live tool count: 21
- Compared with V0.1.1 audit: no added or removed tools observed
- Main V0.2 change: every tool is now classified for safety, budget preview,
  explicit approval, and reporting behavior

## Tools

| Tool | Title | Read-only hint | Destructive hint | Required fields | Relevant parameters |
| --- | --- | --- | --- | --- | --- |
| `list_teams` | List Teams | true | false | none | `response_format` |
| `list_projects` | List Projects | true | false | none | `team_id`, `response_format` |
| `search` | Search Assets & Models | true | false | `target` | `target`, `query`, `public`, `filters`, `image`, `limit`, `offset`, `sort_by`, `team_id`, `project_id`, `response_format` |
| `recommend` | Recommend a Model | true | false | none | `prompt`, `capability`, `features`, `priority`, `max_cost_cu`, `max_latency_seconds`, `limit`, `response_format` |
| `recommend_training` | Recommend Training Base | true | not set | none | `prompt`, `modality`, `dataset_shape`, `style`, `subject`, `priority`, `legacy_ok`, `response_format` |
| `plan_generation` | Plan a Generation Pipeline | true | false | `description` | `description`, `response_format` |
| `get_model_schema` | Get Model Schema | true | false | `model_id` | `model_id`, `team_id`, `project_id`, `response_format` |
| `run_model` | Run Model | false | false | `model_id` | `model_id`, `parameters`, `wait`, `dry_run`, `team_id`, `project_id`, `response_format` |
| `train` | Train Models | false | false | `action`, `model_id` | `action`, `images`, `training_image_id`, `image_data`, `config`, `dry_run`, `team_id`, `project_id`, `response_format` |
| `manage_workflows` | Manage Workflows | false | true | `action` | `action`, `workflow_id`, `workflow_job_id`, `node_id`, `inputs`, `dry_run`, `status`, `flow`, `inputs_definition`, `editor_info`, `team_id`, `project_id`, `response_format` |
| `manage_jobs` | Manage Jobs | false | false | `action` | `action`, `job_id`, `filters`, `limit`, `pagination_token`, `team_id`, `project_id`, `response_format` |
| `display_asset` | Display Asset | true | false | `asset_id` | `asset_id`, `format`, `team_id`, `project_id`, `response_format` |
| `analyze` | Analyze Images | true | false | `action` | `action`, `image`, `text`, `model_id`, `num_results`, `parameters`, `wait`, `dry_run`, `team_id`, `project_id`, `response_format` |
| `upload_asset` | Upload Asset | false | false | `file_name`, `content_type`, `kind` | `file_name`, `content_type`, `kind`, `file_size`, `data`, `team_id`, `project_id`, `response_format` |
| `complete_upload` | Complete Upload | false | false | `upload_id` | `upload_id`, `team_id`, `project_id`, `response_format` |
| `manage_assets` | Manage Assets | false | true | `action` | `action`, `asset_id`, `asset_ids`, `filters`, `metadata`, `tags`, `limit`, `page_token`, `team_id`, `project_id`, `response_format` |
| `manage_models` | Manage Models | false | true | `action` | `action`, `model_id`, `target_project_id`, `data`, `filters`, `tags`, `limit`, `page_token`, `team_id`, `project_id`, `response_format` |
| `manage_collections` | Manage Collections | false | true | `action` | `action`, `collection_id`, `asset_ids`, `model_ids`, `name`, `thumbnail`, `limit`, `page_token`, `team_id`, `project_id`, `response_format` |
| `usage` | Usage | true | false | none | `start_date`, `end_date`, `include`, `team_id`, `project_id`, `response_format` |
| `capture_3d_view` | Capture 3D View | false | false | `image_base64` | `image_base64`, `source_asset_id`, `team_id`, `project_id`, `response_format` |
| `proxy_asset_fetch` | Proxy Asset Fetch | true | false | `asset_id` | `asset_id`, `team_id`, `project_id` |

## Action Details

- `train` actions: `upload_images`, `update_image`, `delete_image`,
  `set_pairs`, `configure`, `start`, `stop`
- `manage_workflows` actions: `list`, `get`, `create`, `update`, `delete`,
  `run`, `approve`, `reject`
- `manage_jobs` actions: `check`, `list`, `cancel`
- `analyze` actions: `caption`, `describe_style`, `detect`, `embed`,
  `prompt_gen`, `translate`
- `manage_assets` actions: `list`, `get`, `update`, `copy`, `download`,
  `delete`, `get_bulk`, `list_tags`, `add_tags`, `remove_tags`
- `manage_models` actions: `list`, `get`, `create`, `update`, `delete`,
  `copy`, `transfer`, `download`, `list_tags`, `add_tags`, `remove_tags`
- `manage_collections` actions: `list`, `get`, `create`, `update`, `delete`,
  `add_assets`, `remove_assets`, `add_models`, `remove_models`
- `usage include` values: `usages`, `usages.daily`,
  `modelUsages.daily`, `nsfwUsages`, `activity`, `entities`

## V0.2 Validation Notes

- `tools/list` returned all 21 tools above.
- `list_teams` returned authenticated data.
- `search` with `target="models"`, `public=true`, and query
  `game asset stylized` returned public models.
- `get_model_schema` returned the live `GPT Image 2` parameter schema.
- `run_model` with `dry_run=true` returned `creativeUnitsCost: 2` and no job.
- `usage` for a one-day range returned a compact totals report.
- `manage_jobs` with `action="check"` returned billing for the existing
  validation job.
- `analyze` with `action="caption"` and `dry_run=true` returned a zero-CU
  estimate for the existing validation asset.
- `recommend_training` returned a Flux 2 LoRA recommendation for an image
  training request using `dataset_shape="single_images"`.
