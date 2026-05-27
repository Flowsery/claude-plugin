# Flowsery — Claude Code Plugin

Query web analytics from Claude Code — **real-time visitors, traffic breakdowns, revenue, goals, and full visitor profiles.** Privacy-first, cookie-free analytics. Alternative to Google Analytics.

## Install

```
/plugin marketplace add flowsery/claude-plugin
/plugin install flowsery
```

## Setup

1. Create an account at [flowsery.com](https://flowsery.com)
2. Add your website and install the tracking snippet
3. Generate a workspace API token at [API Tokens](https://flowsery.com/api-tokens). Workspace tokens start with `flow_ws_` and are the right token type for Claude Code, MCP, OpenClaw, and multi-website analytics access.
4. From inside Claude Code:
   ```
   ./scripts/flowsery.js setup --key flow_ws_xxxxx
   ```

Website API keys start with `flow_` and are scoped to one website. Use them for single-website server-side ingestion such as custom goals or payments. For agent/MCP workflows, use a workspace token, run `./scripts/flowsery.js websites`, then query a site with `--website-id <id>` or `--domain <domain>`.

## What it does

Once installed, Claude can:

- **Overview** — aggregated site metrics (visitors, sessions, bounce rate, revenue)
- **Time series** — trend data by hour, day, week, or month
- **Realtime** — current active visitor count and geographic map
- **Breakdowns** — top pages, referrers, countries, devices, browsers, OS, campaigns, channels (24 dimensions)
- **Visitor profiles** — full journey with identity, activity timeline, and revenue
- **Goal tracking** — track custom events with metadata
- **Revenue tracking** — record payments for attribution (Stripe / LemonSqueezy / Polar auto-tracked)
- **Filters** — drill down by country, device, browser, UTM params, page, channel, and more

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

## License

MIT
