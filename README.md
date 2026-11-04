# Flowsery plugin for Claude Code and Hermes Agent

Query web analytics from Claude Code — **real-time visitors, traffic breakdowns, revenue, goals, and full visitor profiles.** Privacy-first, cookie-free analytics. Alternative to Google Analytics.

## Install in Claude Code

```
/plugin marketplace add flowsery/claude-plugin
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

Once installed, Claude can:

- **Overview**: aggregated site metrics (visitors, sessions, bounce rate, revenue)
- **Time series**: trend data by hour, day, week, or month
- **Realtime**: current active visitor count and geographic map
- **Breakdowns**: top pages, referrers, countries, devices, browsers, OS, campaigns, channels (25 dimensions)
- **Visitor profiles**: full journey with identity, activity timeline, and revenue
- **Goal tracking**: track custom events with metadata
- **Revenue tracking**: record payments for attribution (Stripe / LemonSqueezy / Polar auto-tracked)
- **Filters**: drill down by country, device, browser, UTM params, page, channel, and more
- **AI-detected issues**: list, read and update the status of issues found in session recordings
- **Workspaces**: pick any workspace your sign-in reaches, across organizations

## Example

```
You: How's my traffic this week?
Claude: Your site had 2,847 visitors and 3,912 sessions this week.
        Bounce rate is 62%. Revenue: $1,240 from 18 conversions.
        Top sources: Google (41%), Direct (28%), Twitter (12%).
```

## Alternative: MCP

For Claude Desktop, Cursor, or other MCP-compatible clients:

```json
{
  "mcpServers": {
    "flowsery": {
      "type": "http",
      "url": "https://mcp.flowsery.com/mcp",
      "headers": { "Authorization": "Bearer flow_ws_your_key" }
    }
  }
}
```

## Links

- Product: [flowsery.com](https://flowsery.com)
- AI Agents: [flowsery.com/features/agents](https://flowsery.com/features/agents)
- API Documentation: [flowsery.com/docs/api-introduction](https://flowsery.com/docs/api-introduction)
- API Tokens: [flowsery.com/api-tokens](https://flowsery.com/api-tokens)
- Privacy policy: [flowsery.com/privacy](https://flowsery.com/privacy)

## License

MIT
