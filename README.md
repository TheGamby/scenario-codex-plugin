# Scenario Codex Plugin

Codex plugin for using Scenario from Codex through the official Scenario MCP
server.

This V1 plugin is intentionally small:

- it registers the official Scenario MCP endpoint through `mcp-remote`
- it uses Scenario OAuth as the supported authentication flow
- it adds a Codex skill that guides model discovery, generation planning, asset
  handling, uploads, and training workflows
- it does not store Scenario API keys or add an API-key wrapper
- it keeps personal Codex marketplace entries outside the repository

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

## Using From This Repo

Use this repository as the plugin root:

```text
/Volumes/Development/source/gamby/scenario-codex-plugin
```

The plugin manifest is at:

```text
.codex-plugin/plugin.json
```

Validated local paths:

1. Open this repository as the active Codex workspace.
2. Keep the workspace trusted in Codex.
3. Use the repo root as the local plugin root when adding a local plugin.
4. Verify that Codex reads `.codex-plugin/plugin.json`, `.mcp.json`, and
   `skills/` from this checkout.

The repository does not contain personal marketplace files. If you want the
plugin to appear in a personal Codex marketplace, add that integration
separately so the repo remains the source of truth for the plugin files. Do not
commit machine-local marketplace files unless you intentionally create a
repo/team marketplace.

For local MCP-only testing from this checkout, `.codex/config.toml` mirrors the
same Scenario MCP server configuration. This is useful for validating the MCP
connection without modifying personal marketplace files.

Implementation progress and open approval-gated items are tracked in
`planing/status.md`.

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

## Validated Setup

Validated on 2026-05-24 from this repository:

- `node --version`: `v25.9.0`
- `npx --version`: `11.12.1`
- `mcp-remote`: `0.1.37` as resolved by `npx -y mcp-remote@latest`
- Scenario MCP server: `scenario-mcp` `0.9.0`
- Plugin validator: `Plugin validation passed`
- Read-only authenticated check: `list_teams` returned authenticated
  team/project data; exact account-specific names, IDs, and counts are omitted
  from this public README
- Read-only workflow check: `search` with `target="models"`, `public=true`,
  and query `game asset stylized` returned public models including `Gem Icons
  v2`, `Game Backgrounds - III`, and `Environment Sprites 2.0`
- Planning workflow check: `plan_generation` returned a text-to-image pipeline
  using `run_model` for a stylized fantasy game asset icon
- Phase 4 dry-run check: for project `Tartaros`, `get_model_schema` was read
  for `GPT Image 2`, then `run_model` was called with `dry_run=true` for a
  512x512 low-quality single-output icon test. Scenario returned
  `creativeUnitsCost: 2`, `creativeUnitsDiscount: 0`, and no job was created.
- Phase 4 generation check: a first 512x512 real run failed because `GPT Image
  2` requires at least 655,360 pixels. A follow-up 1024x1024 low-quality
  single-output run succeeded with job `job_shzQW61m5EoSkNZz6M8tdUmG` and asset
  `asset_GQDDdE8VgMwqFfNQ7JVvATyk`. `display_asset` returned the generated
  image.
- Personal marketplace check: `~/.agents/plugins/marketplace.json` contains a
  `scenario-codex-plugin` entry, and `~/plugins/scenario-codex-plugin` is a
  symlink to this repository.

The default user npm cache on the test machine was not writable, so validation
used the same temporary npm cache configured in `.mcp.json`.

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
6. Run generation only when the requested action is clear.
7. Display generated assets when possible, or report asset and job identifiers.
8. Use `manage_jobs` for long-running job status checks.

Uploads, training, and workflow execution should be treated as explicit user
actions, not as side effects of discovery or planning.

## Example Prompts

- `List my Scenario projects.`
- `Find a Scenario model for stylized fantasy game icons.`
- `Plan a Scenario image generation for a blue crystal inventory icon.`
- `Dry-run a small GPT Image 2 icon generation and report the estimated Creative Units.`
- `Show the generated Scenario asset for this asset ID.`

## Minimal Dry-Run Workflow

This workflow checks cost and parameter shape without starting a generation:

1. Use `list_teams` and `list_projects` to confirm the target project.
2. Use `search`, `recommend`, or `plan_generation` to choose a model.
3. Call `get_model_schema` for the selected `model_id`.
4. Call `run_model` with `dry_run=true`, confirmed `team_id` and `project_id`,
   and parameters that match the schema.
5. Review the returned `creativeUnitsCost` before deciding whether to run the
   same request without `dry_run`.

The validated dry-run used `GPT Image 2` for a small stylized fantasy inventory
icon and estimated 2 Creative Units. A real 512x512 run failed because the live
service requires at least 655,360 pixels for this model, so the successful
minimal generation used 1024x1024.

Successful generation:

- Model: `GPT Image 2`
- Parameters: `numOutputs=1`, `width=1024`, `height=1024`, `quality=low`,
  `background=opaque`
- Job: `job_shzQW61m5EoSkNZz6M8tdUmG`
- Asset: `asset_GQDDdE8VgMwqFfNQ7JVvATyk`
- Result display: `display_asset` returned the generated image

## Personal Marketplace Entry

This plugin can be loaded from the repository without copying its files by using
a personal marketplace entry and a symlink:

```text
~/.agents/plugins/marketplace.json
~/plugins/scenario-codex-plugin -> /Volumes/Development/source/gamby/scenario-codex-plugin
```

The marketplace entry points to `./plugins/scenario-codex-plugin` with
`policy.installation: "AVAILABLE"` and `policy.authentication: "ON_INSTALL"`.
This keeps the repository as the source of truth.

## API-Key Auth

API-key authentication is intentionally not part of V1/V0.1.1. OAuth worked in
Codex during validation, so the optional API-key phase is closed as
"not implemented". This avoids storing secrets in plugin configuration and keeps
OAuth as the supported path. A future version may add a wrapper that reads
`SCENARIO_API_KEY` and `SCENARIO_API_SECRET` from the environment if OAuth
becomes impractical for non-interactive workflows.

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
- Tool names or schemas differ from examples: Scenario MCP is beta, so inspect
  the live MCP tool descriptions and follow the current server contract.

## Release And Maintenance

- Current release: `v0.1.1`
- Keep OAuth as the default authentication mode.
- Re-run plugin validation after any manifest, MCP, or skill change.
- Re-check live Scenario MCP tool names and schemas before changing workflow
  guidance.
- Track future maintenance in GitHub issues, or in
  `planing/follow-up-issues.md` when issue creation is unavailable, rather than
  expanding this plugin into a custom Scenario API client.

## References

- Scenario MCP: <https://mcp.scenario.com/>
- Scenario MCP endpoint: <https://mcp.scenario.com/mcp>
- Scenario GitHub repo target:
  <https://github.com/TheGamby/scenario-codex-plugin>
