# Flowsery plugin for Claude Code and Hermes Agent

Ask Flowsery, the privacy-first, cookie-free web analytics, about live visitors, traffic breakdowns, goals, revenue, visitor journeys and the bugs its AI found in session recordings, from Claude Code or Hermes Agent.

## What this repo is

This repo is the Flowsery plugin for Claude Code and Hermes Agent, and nothing else. Both read the same package: `.claude-plugin/plugin.json`, `.mcp.json` and `.claude-plugin/marketplace.json` for Claude Code, `plugin.json` and `mcp.json` for Hermes, and one skill in [`skills/flowsery/`](./skills/flowsery). The plugin connects to the hosted MCP server at `https://mcp.flowsery.com/mcp`, signs in with OAuth and never asks for an API key. Its skill only calls MCP tools and ships no script, because the Claude plugin directory and the Hermes catalog both review for that.

Everything else lives in [Flowsery/agent](https://github.com/Flowsery/agent): the agent skill for `npx skills add` (a CLI script with an API key), the source of the MCP server, and the Gemini CLI extension.

| You use | Install | Sign-in |
| --- | --- | --- |
| Claude Code | `/plugin install flowsery --marketplace Flowsery/claude-plugin` | OAuth |
| Hermes Agent | `hermes plugins install flowsery` | OAuth |
| Gemini CLI | `gemini extensions install https://github.com/Flowsery/agent` | OAuth |
| Claude Desktop, Cursor, ChatGPT or any MCP client | Add `https://mcp.flowsery.com/mcp` as a remote server | OAuth, or an API key header |
| Cursor, Codex, Windsurf, Claude Code or any agent with skills | `npx skills add Flowsery/agent` | API key |

## Install in Claude Code

```
/plugin install flowsery --marketplace Flowsery/claude-plugin
```

On Claude Code older than 2.1.275, add the marketplace first:

```
/plugin marketplace add Flowsery/claude-plugin
/plugin install flowsery
```

## Setup

1. Create an account at [flowsery.com](https://flowsery.com), add your website and install the tracking snippet.
2. In Claude Code, run `/mcp`, pick `flowsery` and sign in with the email you use on Flowsery.

The plugin talks to Flowsery only through its MCP server at `mcp.flowsery.com`, signed in with OAuth. It never asks for an API key and reads nothing from your environment or config files.

## Install in Hermes Agent

You need Hermes Agent 0.21.6 or newer and Node.js 20.18.1 or newer with `npx` on your `PATH`.

From the Hermes plugin catalog:

```bash
hermes plugins install flowsery
hermes plugins enable flowsery
```

Or straight from GitHub:

```bash
hermes plugins install Flowsery/claude-plugin --no-enable
hermes plugins enable flowsery
```

Start a new session. The first time the server starts, your browser opens the Flowsery sign-in page. Sign in with the email you use on Flowsery and approve. The token is kept and refreshed, so you sign in once. If the tools don't show up after you sign in, run `/reload-mcp`.

The Hermes package is `plugin.json`, `mcp.json` and the skill in `skills/flowsery/`. It ships no code of its own. Before you install, know that:

- `mcp.json` runs the pinned `mcp-remote@0.1.49` through `npx`, which downloads it from npm on first use. It opens a local callback port for the sign-in and stores the OAuth tokens in `~/.mcp-auth`.
- Every tool call goes to `mcp.flowsery.com` and acts on your Flowsery account, with your own role in each workspace. The server decides the tool set at runtime, so the pinned commit doesn't cover it.
- `plugin.json` marks the server `untrusted`. Hermes asks before every tool the server doesn't mark read-only (tracking a goal, deleting goals and changing an issue's status), and refuses those tools in cron and other unattended runs.
- `track_goal` records an event in your analytics, and `delete_goals` removes goal events for good.
- The package sends no telemetry.

On a server or another machine without a browser, skip the plugin and add the server to `~/.hermes/config.yaml` with an API key from [flowsery.com/api-tokens](https://flowsery.com/api-tokens):

```yaml
mcp_servers:
  flowsery:
    url: "https://mcp.flowsery.com/mcp"
    headers:
      Authorization: "Bearer ${FLOWSERY_API_KEY}"
```

Put `FLOWSERY_API_KEY=...` in `~/.hermes/.env`. The key works like a password for your Flowsery account, so don't paste it into a chat.

## What it does

Once installed, Claude or Hermes can:

- Headline totals for any date range: visitors, sessions, bounce rate, revenue and conversion rate
- Trends by hour, day, week or month
- Visitors active right now, with a live map
- Breakdowns by any of 25 dimensions: pages, landing pages, referrers, channels, UTM campaigns, countries, regions, cities, devices, browsers and more
- Filters on country, device, browser, UTM parameters, page and channel
- Goals and their completions, and recording or deleting custom goal events
- One visitor's journey with their activity timeline
- The issues Flowsery's AI found in session recordings, and changing their status
- Work in any workspace your sign-in reaches, across organizations

## Example

```
You: How's my traffic this week?
Agent: Your site had 2,847 visitors and 3,912 sessions this week.
       Bounce rate is 62%. Revenue: $1,240 from 18 conversions.
       Top sources: Google (41%), Direct (28%), Twitter (12%).
```

## Links

- Product: [flowsery.com](https://flowsery.com)
- AI agents: [flowsery.com/features/agents](https://flowsery.com/features/agents)
- Skill, MCP server and Gemini extension: [Flowsery/agent](https://github.com/Flowsery/agent)
- Privacy policy: [flowsery.com/privacy](https://flowsery.com/privacy)

## License

MIT
