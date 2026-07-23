# BlueHive HUM Skills

Public AI agent skills for working with [BlueHive HUM](https://hum.bluehive.com/).

## Install

```bash
npx skills add https://github.com/bluehive-health/hum-skills --skill bluehive-hum
```

Then connect your agent to HUM's remote MCP server by following the [AI Agent Setup guide](https://hum.bluehive.com/docs/agent-setup).

## Available skills

- `bluehive-hum` — safely discover and execute HUM workflows for voice and SMS agents, calls, phone numbers, campaigns, contacts, integrations, analytics, and organization provisioning.

## Security

Never commit or include a `hum_live_*` API key in an issue, pull request, prompt, or configuration file. Store credentials in your agent's secure input or environment-variable system and revoke exposed keys immediately.
