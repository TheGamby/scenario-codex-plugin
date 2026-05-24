# Scenario Codex Plugin

Codex plugin for using Scenario from Codex through the official Scenario MCP
server.

This V1 plugin is intentionally small:

- it registers the official Scenario MCP endpoint through `mcp-remote`
- it uses Scenario OAuth as the supported authentication flow
- it adds a Codex skill that guides model discovery, generation planning, asset
  handling, uploads, and training workflows
- it does not store Scenario API keys or add an API-key wrapper
- it does not modify any personal Codex marketplace files

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

The plugin does not create a personal marketplace entry by default. If you want
it to appear in a personal Codex marketplace, add that integration separately so
the repo remains the source of truth for the plugin files. Do not commit
machine-local marketplace files unless you intentionally create a repo/team
marketplace.

For local MCP-only testing from this checkout, `.codex/config.toml` mirrors the
same Scenario MCP server configuration. This is useful for validating the MCP
connection without modifying personal marketplace files.

Implementation progress and open approval-gated items are tracked in
`planing/status.md`.

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

The default user npm cache on the test machine was not writable, so validation
used the same temporary npm cache configured in `.mcp.json`.

## Expected Workflow

When working with Scenario through Codex:

1. Discover teams and projects if the target context is unclear.
2. Use `list_teams` first, then `list_projects` when a team is known but the
   project is not.
3. Search or request model recommendations for open-ended generation requests.
4. Inspect the selected model schema before running generation.
5. Run generation only when the requested action is clear.
6. Display generated assets when possible, or report asset and job identifiers.

Uploads, training, and workflow execution should be treated as explicit user
actions, not as side effects of discovery or planning.

## API-Key Auth

API-key authentication is not part of V1. This avoids storing secrets in plugin
configuration and keeps OAuth as the supported path. A future version may add a
wrapper that reads `SCENARIO_API_KEY` and `SCENARIO_API_SECRET` from the
environment.

## Troubleshooting

- `npx` not found: install Node.js/npm and reopen Codex.
- npm cache `EPERM`: keep the `npm_config_cache` value from `.mcp.json`, or
  repair ownership of your user-level npm cache outside this repository.
- OAuth does not start: retry the Scenario MCP action and check that pop-up or
  browser handoff is allowed.
- Authentication succeeds but writes fail: verify Scenario team/project
  permissions and whether the account can perform generation actions.
- Tool names or schemas differ from examples: Scenario MCP is beta, so inspect
  the live MCP tool descriptions and follow the current server contract.

## References

- Scenario MCP: <https://mcp.scenario.com/>
- Scenario MCP endpoint: <https://mcp.scenario.com/mcp>
- Scenario GitHub repo target:
  <https://github.com/TheGamby/scenario-codex-plugin>
