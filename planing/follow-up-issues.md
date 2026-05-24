# Scenario Codex Plugin - Follow-Up Issues

These are GitHub-ready maintenance issues for V0.2.0. They are kept in the repo
because direct GitHub issue creation previously returned
`403 Resource not accessible by integration`.

## Re-check Scenario MCP tool schemas after upstream changes

Scenario MCP is beta and live tool names, schemas, minimum parameter
constraints, annotations, action enums, and billing behavior can change.

Tasks:

- Re-run plugin validation.
- Re-run live `tools/list` discovery.
- Compare all tools in `planing/scenario-mcp-tool-inventory-v0.2.md`.
- Re-check action enums for `train`, `manage_workflows`, `manage_jobs`,
  `analyze`, `manage_assets`, `manage_models`, and `manage_collections`.
- Update `skills/scenario/SKILL.md`, `README.md`, and the tool matrix when
  tool contracts change.

Acceptance:

- Documentation and skill guidance match the current Scenario MCP contract.
- The plugin still delegates to Scenario MCP and does not add a custom Scenario
  API client.

## Add disposable upload smoke test

V0.2 documents upload behavior but deliberately does not upload a real file as
part of release validation.

Tasks:

- Prepare a small non-sensitive public-domain image fixture.
- Use `upload_asset` with multipart upload and `complete_upload`.
- Verify the returned asset with `display_asset` or `manage_assets get`.
- Document the result without committing local paths, presigned URLs, team IDs,
  or project IDs.

Acceptance:

- Upload flow is validated end to end with a disposable asset.
- Public documentation contains only sanitized evidence.

## Add training dry-run smoke test

V0.2 validates `recommend_training` but does not configure or start training.

Tasks:

- Create or select a disposable model target suitable for dry-run validation.
- Run `recommend_training` for the intended dataset shape.
- Run `train` with `dry_run=true` for `configure` or `start` if the live MCP
  accepts the test setup.
- Do not start real training without explicit approval.

Acceptance:

- A training cost-preview path is validated without consuming training credits.
- Any required disposable IDs are kept out of public docs.

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
