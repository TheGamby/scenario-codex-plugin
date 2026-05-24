---
name: scenario
description: Use Scenario through the official Scenario MCP server for model discovery, asset generation planning, project context, uploads, training, and game-art workflows from Codex.
---

# Scenario

Use this skill when the user asks about Scenario, Scenario MCP, Scenario models,
asset generation, game-art generation, asset upload, model training, or creative
workflows that should run through Scenario.

## Default Approach

- Use the official `scenario` MCP server exposed by this plugin.
- Treat OAuth as the supported V1 authentication flow. Do not ask for or store
  Scenario API keys for this plugin.
- Start with read-only discovery when the user's team, project, model, or
  workflow context is unclear.
- Prefer Scenario MCP tools over direct Scenario API calls.

## Workflow

1. Establish context.
   - If the team is unknown, call `list_teams` first.
   - If the team is known but the project is unknown, call `list_projects`.
   - If multiple teams or projects are returned, ask the user which one to use
     before continuing. Do not pick a project automatically.
   - If the user gives a project, model, asset, or workflow name, resolve it to
     the Scenario identifier required by the MCP tool before taking action.

2. Choose the model deliberately.
   - For open-ended generation requests, use `recommend` for one-step
     generations, `plan_generation` for multi-step pipelines, or `search` when
     private/public model discovery by query is needed.
   - Before calling a model, call `get_model_schema` for the selected
     `model_id`.
   - Match user-facing prompt details to the model schema instead of guessing
     unsupported fields.

3. Run generation only when the intent is clear.
   - Confirm destructive, paid, upload, training, or workflow-starting actions
     when the user has not explicitly requested them.
   - Do not trigger training, workflow execution, or asset upload as a side
     effect of a planning or discovery request.
   - When checking feasibility or cost before a real generation, prefer
     `run_model` with `dry_run=true` if the live tool schema supports it.
   - For OAuth callers, provide the confirmed `team_id` and `project_id` when
     calling generation or job tools.

4. Report useful results.
   - After generation, show or display generated assets when the MCP tool
     supports it.
   - If assets cannot be displayed directly, report stable asset IDs, job IDs,
     model names, and next steps.
   - For long-running jobs, return the job identifier and use `manage_jobs` to
     check status when the user asks for progress.

## Uploads And Training

- Use Scenario's upload flow exactly as exposed by MCP. If the flow requires a
  start/upload/complete sequence, do not skip the completion step.
- Treat upload, training, and workflow execution as explicit user actions.
- Preserve the user's requested project and team boundaries when creating or
  modifying Scenario resources.

## Troubleshooting

- If the Scenario MCP server is unavailable, first check that Node.js and `npx`
  are installed.
- If authentication fails, direct the user to complete the Scenario OAuth flow
  and verify their Scenario account permissions.
- If a generation or write action is denied, check Scenario project/team access
  and whether the account can perform generation actions.
- Scenario MCP is beta; if a tool or schema differs from this guidance, inspect
  the live MCP tool descriptions and follow the current server contract.
