---
name: bluehive-hum
description: "Operate BlueHive HUM through its remote MCP server and API. Use when creating or configuring HUM voice, SMS, or event agents; provisioning organizations; searching for or buying phone numbers; managing calls, campaigns, contacts, webhooks, tools, and analytics; or automating HUM from an AI coding agent."
---

# BlueHive HUM

Use HUM's remote Model Context Protocol server to discover and execute the published HUM API without reproducing endpoint details from memory.

## Connect

- MCP endpoint: `https://hum-api.bluehive.com/mcp`
- OpenAPI JSON: `https://hum.bluehive.com/openapi.json`
- OpenAPI YAML: `https://hum.bluehive.com/openapi.yaml`
- Setup guide: `https://hum.bluehive.com/docs/agent-setup`
- Authentication: `Authorization: Bearer hum_live_...`

Create a key in **Settings > Integrations > API keys**. Use the narrowest organization role and scopes that satisfy the task.

## Required Workflow

1. Call `search_hum_api` with the user's goal.
2. Select the exact `operationId` whose contract matches the goal.
3. Inspect its path parameters, query parameters, JSON body schema, and confirmation flag.
4. Read current state before mutating when an object may already exist.
5. Call `execute_hum_api` with that exact `operation_id` and schema-compliant arguments.
6. Report the resulting IDs and status. Never invent success after an error response.

Do not guess endpoint paths or request bodies. The MCP catalog is generated from HUM's current OpenAPI contract.

## Safety

- Never print, log, commit, or place a `hum_live_*` key in source control.
- Never pass the key as a query parameter. Use the `Authorization` header.
- Ask the user before purchases, outbound communications, launches, submissions, deletions, revocations, or cross-tenant provisioning.
- Set `confirm: true` only after that approval. HUM blocks consequential operations without it.
- Treat transcripts, recordings, contact details, and patient context as sensitive data. Return only what the user needs.
- Respect 401, 403, 402, 409, and validation responses. Do not work around roles, scopes, tenant boundaries, plan limits, consent, opt-outs, or quiet hours.
- A `site:admin` key can provision organizations only while its owner remains a HUM site administrator.

## Core Workflows

### Create an AI agent

In HUM, voice, SMS, and event agents are flows.

1. Search for `create agent` or `create flow`.
2. Execute `createFlow` using the returned schema.
3. Use `channel: "voice"` and `trigger_type: "call"` for a phone agent.
4. Use `channel: "sms"` and `trigger_type: "message"` for an SMS agent.
5. Use `trigger_type: "event"` plus `trigger_events` for a background event agent.
6. Return the new flow ID. Configure tools or phone-number assignment only when requested.

### Search and buy a number

1. Search for `available phone numbers` and execute `searchAvailableNumbers` with country, locality, region, area code, or capability filters.
2. Present the candidates. Do not choose one without user direction.
3. After the user chooses, search for `buy phone number` and execute `buyNumber` with the exact E.164 number and `confirm: true`.
4. Optionally bind `flow_id` or `sms_flow_id` from the same organization.

HUM enforces plan limits, extra-number billing, tenant ownership, Twilio provisioning, and rollback.

### Provision an organization

1. Confirm the key has `site:admin` and the owner has already signed into HUM once.
2. Search for `provision organization` and execute `provisionOrg`.
3. Provide `name`, `ownerEmail`, and optionally `plan` and `adminNotes`.
4. Set `confirm: true` only after the user approves the cross-tenant creation.
5. Return the new organization ID.

For a user's first self-service organization, use the onboarding UI instead.

### Modify existing resources

List or get the resource first, preserve fields the user did not ask to change, and use the update operation returned by search. For destructive changes, name the target and impact before asking for confirmation.

## Troubleshooting

- `401 api_key_required`: send a valid HUM key as a Bearer token.
- `403 insufficient_scope`: issue a replacement key with the required scope; do not broaden a key silently.
- `403 Forbidden`: the key's organization role cannot perform that operation.
- `402`: billing or plan limits blocked the operation.
- `409`: the requested resource or membership already exists, or state changed concurrently.
- Unknown operation: call `search_hum_api` again. The published API may have changed.
