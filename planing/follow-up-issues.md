# Scenario Codex Plugin - Follow-Up Issues

These are GitHub-ready maintenance issues for V0.1.1. They are kept in the repo
because the GitHub connector returned `403 Resource not accessible by
integration` when issue creation was attempted.

## Re-check Scenario MCP tool schemas after upstream changes

Scenario MCP is beta and live tool names, schemas, minimum parameter
constraints, and annotations can change.

Tasks:

- Re-run plugin validation.
- Re-run live `tools/list` discovery.
- Compare key workflow tools: `list_teams`, `list_projects`, `search`,
  `recommend`, `plan_generation`, `get_model_schema`, `run_model`,
  `display_asset`, and `manage_jobs`.
- Update `skills/scenario/SKILL.md` and `README.md` when tool contracts change.

Acceptance:

- Documentation and skill guidance match the current Scenario MCP contract.
- The plugin still delegates to Scenario MCP and does not add a custom Scenario
  API client.

## Evaluate API-key mode only if OAuth becomes impractical

OAuth worked in the Codex desktop validation flow. API-key support should remain
out of scope unless a non-interactive or repeated OAuth failure case appears.

Tasks if this becomes necessary:

- Add a wrapper script that reads `SCENARIO_API_KEY` and
  `SCENARIO_API_SECRET` from the environment.
- Do not store secrets in `.mcp.json`, README examples, or committed files.
- Keep OAuth as the default path.
- Add clear missing-environment-variable errors.

Acceptance:

- API-key mode works without committed secrets.
- OAuth remains documented as the default.

## Keep marketplace setup local and reproducible

The validated personal marketplace setup uses a local symlink and a personal
`~/.agents/plugins/marketplace.json` entry. These are intentionally not repo
files.

Tasks:

- Keep README instructions current with Codex marketplace conventions.
- Do not commit machine-local marketplace files.
- If Codex adds a repo-native local plugin install path, update README and
  remove symlink-specific assumptions where appropriate.

Acceptance:

- New users can reproduce local plugin loading from the README.
- The repo remains the source of truth for plugin files.
