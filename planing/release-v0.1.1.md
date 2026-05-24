# Scenario Codex Plugin - Release V0.1.1

Release date: 2026-05-24

## Scope

V0.1.1 closes all phases from `planing/planing.md` for the small Scenario Codex
plugin.

## Included

- Codex plugin manifest for `scenario-codex-plugin`.
- Scenario MCP configuration through `npx -y mcp-remote@latest`.
- OAuth-first authentication guidance.
- Scenario skill for discovery-first workflows.
- Local Codex marketplace setup documented and validated.
- Read-only Scenario MCP validation.
- Model schema, dry-run, and real generation validation.
- Troubleshooting for npm cache, OAuth, permissions, schema drift, and minimum
  generation resolution.

## Deliberately Not Included

- No custom Scenario REST API client.
- No stored Scenario API secrets.
- No API-key wrapper while OAuth remains practical.
- No upload, training, or workflow automation.
- No asset viewer beyond Scenario MCP `display_asset`.

## Validation Evidence

- Plugin validator: `Plugin validation passed`.
- Scenario MCP server: `scenario-mcp` `0.9.0`.
- Successful generation job: `job_shzQW61m5EoSkNZz6M8tdUmG`.
- Generated asset: `asset_GQDDdE8VgMwqFfNQ7JVvATyk`.
- Personal marketplace entry:
  `~/.agents/plugins/marketplace.json`.
- Plugin symlink:
  `~/plugins/scenario-codex-plugin -> /absolute/path/to/scenario-codex-plugin`.

## Follow-Up Planpoints

- Re-check Scenario MCP schemas after upstream changes.
- Evaluate API-key mode only if OAuth becomes impractical.
- Keep local Marketplace setup documented without committing machine-local
  Marketplace files.

Direct GitHub issue creation was attempted during release closure, but the
GitHub connector returned `403 Resource not accessible by integration`. The
follow-ups are therefore tracked in `planing/follow-up-issues.md` until they can
be copied into GitHub issues.
