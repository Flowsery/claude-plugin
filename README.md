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

## Example prompts

- "How's my traffic this week compared to last week?"
- "Which channels and campaigns brought paying customers this month?"
- "Visitors dropped on Tuesday. Check pages, referrers and devices and tell me why."
- "How many people are on the site right now, and where are they?"
- "How much traffic did ChatGPT, Perplexity and other AI assistants send us this month?"
- "What did Flowsery find in session recordings this week? Mark the checkout bug as in progress."
- "Show me the journey of the visitor who just started a free trial."
- "Which landing pages convert best for mobile visitors from Germany?"

## Tools

26 tools. Every report takes a date range, a timezone and filters on country, region, city, device, browser, OS, page, hostname, referrer, channel and UTM parameters. Recording payments for revenue attribution is in the agent skill, not the MCP server.

### Workspaces and websites

| Tool | What it does | Changes data |
| --- | --- | --- |
| `list_workspaces` | List the workspaces the sign-in can act in; pass an id as `workspaceId` to any other tool | No |
| `list_websites` | List the websites the token can read, with domain, timezone, currency and KPI goal. Call it first | No |
| `get_metadata` | One website's settings: domain, timezone, currency, KPI goal, logo and colors | No |

### Traffic

| Tool | What it does | Changes data |
| --- | --- | --- |
| `get_overview` | Headline totals for a date range: visitors, sessions, bounce rate, session duration, revenue, revenue per visitor and conversion rate | No |
| `get_timeseries` | The same metrics by hour, day, week or month, with new, renewal and refunded revenue | No |
| `get_realtime` | Visitors active in the last 5 minutes | No |
| `get_realtime_map` | Active visitors with city-level location for a live map; coordinates rounded to 0.1 degrees, no IP addresses | No |

### Breakdowns

| Tool | What it does | Changes data |
| --- | --- | --- |
| `get_pages` | Page paths ranked by visitors | No |
| `get_referrers` | Referring sites ranked by visitors, with known sources such as Google, ChatGPT and Reddit named | No |
| `get_channels` | Traffic grouped into GA4-style channels: Organic Search, Paid Social, Email, Direct and more | No |
| `get_campaigns` | UTM campaigns ranked by visitors | No |
| `get_countries` | Visitors by country | No |
| `get_regions` | Visitors by region or state | No |
| `get_cities` | Visitors by city | No |
| `get_devices` | Desktop, mobile and tablet split | No |
| `get_browsers` | Visitors by browser | No |
| `get_operating_systems` | Visitors by operating system | No |
| `get_hostnames` | Visitors by hostname, for sites that track several domains or subdomains | No |
| `get_breakdown` | Group visitors by any of 25 dimensions, including entry page, exit link, browser and OS versions and every UTM parameter | No |

### Goals and visitors

| Tool | What it does | Changes data |
| --- | --- | --- |
| `get_goals` | Every goal, including the automatic payment and free trial goals, with completions in the window | No |
| `track_goal` | Record one completion of a custom goal; the goal is created on first use | Yes |
| `delete_goals` | Permanently delete goal completions that match the filters | Yes |
| `get_visitor` | One visitor's location, device, source, visits, goals, revenue and timeline | No |

### Session issues

| Tool | What it does | Changes data |
| --- | --- | --- |
| `list_issues` | Bugs, broken flows and UX problems the AI found in session recordings, ranked by severity | No |
| `get_issue` | One issue with its occurrences, sessions, steps to replicate, comments and linked Linear or Jira ticket | No |
| `update_issue_status` | Set an issue to open, in progress, resolved or suspended | Yes |

## Roles, permissions and safety

- Every call runs with the signed-in member's role in the workspace. Reads need `workspace.read`; tracking or deleting goals and changing an issue's status need `flowsery.write`, which Admin and Editor hold.
- Read-only tools carry `readOnlyHint`. Only `track_goal`, `delete_goals` and `update_issue_status` change anything, so clients that confirm writes ask before those three.
- Visitor tools return the analytics of your own websites. `get_realtime_map` rounds coordinates to 0.1 degrees, and no tool returns IP addresses or billing details.
- A website key (`flow_`) reaches one website; a workspace token (`flow_ws_`) or an OAuth sign-in reaches every website in the workspace.

## FAQ

**Do I need an API key?**
No. Point your client at `https://mcp.flowsery.com/mcp` and sign in with the email you use on Flowsery. Keys are for headless agents and clients without OAuth.

**Does the agent see personal data?**
It sees what your Flowsery dashboard shows for your own websites: approximate location, device, source and activity. No IP addresses, and live map coordinates are rounded.

**Can it change my analytics?**
Only three tools write: `track_goal`, `delete_goals` and `update_issue_status`. Everything else is read-only.

**Does it work with several websites?**
Yes. Call `list_websites` and pass a `websiteId` or `domain` to the report tools.

**Can I use Flowsery outside Claude Code and Hermes?**
Yes. Claude Desktop, ChatGPT, Cursor, Windsurf, VS Code, Codex and Gemini CLI connect to the same server at `https://mcp.flowsery.com/mcp`. The setup for each is in [Flowsery/agent](https://github.com/Flowsery/agent#setup-by-client).

## Links

- Product: [flowsery.com](https://flowsery.com)
- AI agents: [flowsery.com/features/agents](https://flowsery.com/features/agents)
- Skill, MCP server and Gemini extension: [Flowsery/agent](https://github.com/Flowsery/agent)
- Privacy policy: [flowsery.com/privacy](https://flowsery.com/privacy)

## License

MIT
