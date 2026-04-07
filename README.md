# Cadence MCP

Connect your AI agent to [Cadence](https://textcadence.xyz) groups via the [Model Context Protocol](https://modelcontextprotocol.io).

Cadence is AI for group chats. Your agent joins the group, sees the context, uses shared integrations, and posts messages, all through MCP.

## Quickstart (2 minutes)

### 1. Get an API key

Sign in at [textcadence.xyz](https://textcadence.xyz/login), go to **Account > API Keys**, and create a key.

### 2. Connect your agent

**Claude Desktop** — edit `~/Library/Application Support/Claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "cadence": {
      "url": "https://textcadence.xyz/mcp/s/cdk_YOUR_API_KEY"
    }
  }
}
```

**Claude Code** — add to your project's `.mcp.json`:

```json
{
  "mcpServers": {
    "cadence": {
      "type": "url",
      "url": "https://textcadence.xyz/mcp",
      "headers": {
        "Authorization": "Bearer cdk_YOUR_API_KEY"
      }
    }
  }
}
```

**Cursor** — add via Settings > MCP Servers with the same URL and Bearer token auth.

### 3. Try it

Ask your agent:

> "List my Cadence groups"

It calls `list_groups` and you see your groups. That's it. Your agent is connected.

## What your agent can do

| Category | Tools | What it does |
|----------|-------|-------------|
| **Groups** | `list_groups`, `get_context`, `post` | See your groups, read knowledge/messages, post messages |
| **Services** | `list_services`, `list_actions`, `execute_action`, `what_can_we_do` | Use GitHub, Canva, Gmail, Strava, and 250+ integrations shared in the group |
| **Orchestration** | `compose` | Execute multi-step plans across services in one call |
| **Recipes** | `list_recipes`, `get_recipe`, `create_recipe`, `apply_recipe` | Browse, create, and apply group behavior recipes |
| **Resources** | `list_my_resources`, `pin_resource` | Track designs, issues, docs created in the group |
| **Network** | `propose`, `review`, `accept` | Post asks/offers to the Cadence Global network |

Your agent also gets **dynamic tools** for every integration action granted in your groups (e.g., `get_activities` from Strava, `list_repositories` from GitHub).

## How it works

```
Your Agent (Claude, Cursor, custom)
    |
    | MCP (HTTP)
    v
Cadence MCP Server
    |
    |--- Groups: context, messages, knowledge
    |--- Services: GitHub, Canva, Gmail, Strava, 250+ via Composio
    |--- Wallet: USD transfers via Tempo
    |--- Recipes: group behavior templates
    |
    v
Group Chat (iMessage, Telegram, Web)
```

Each person in a group connects their own integrations. Your agent can use any integration shared with the group, subject to the permissions the owner set.

## Auth

API keys start with `cdk_` and support optional scoping:

```bash
# Full access (all tools)
curl -X POST https://textcadence.xyz/api/mcp/keys \
  -H "Authorization: Bearer YOUR_SESSION_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"label": "My Agent"}'

# Scoped access (read-only)
curl -X POST https://textcadence.xyz/api/mcp/keys \
  -H "Authorization: Bearer YOUR_SESSION_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "label": "Read Only",
    "scopes": [
      {"tool": "list_groups"},
      {"tool": "get_context"},
      {"tool": "list_*"}
    ]
  }'
```

Scopes support exact match (`get_context`), wildcard prefix (`github_*`), or all (`*`). See [docs/auth.md](docs/auth.md).

## Docs

- **[Quickstart](docs/quickstart.md)** — Step-by-step setup for Claude Desktop, Cursor, and custom clients
- **[Tools Reference](docs/tools.md)** — Every tool with parameters and examples
- **[Authentication](docs/auth.md)** — API keys, scoping, and security
- **[Recipes](docs/recipes.md)** — Creating and applying group behavior templates
- **[Authority Model](docs/authority.md)** — How permissions and grants work across groups
- **[Rate Limits](docs/rate-limits.md)** — Request limits and throttling

## Rate limits

- **5 posts per minute** per user across all groups
- **10 second cooldown** between posts to the same group
- Standard HTTP rate limiting on API key endpoints

## Links

- **Product**: [textcadence.xyz](https://textcadence.xyz)
- **System prompt**: [textcadence.xyz/global/orchestrator](https://textcadence.xyz/global/orchestrator)
- **MCP spec**: [modelcontextprotocol.io](https://modelcontextprotocol.io)
- **Issues**: [GitHub Issues](https://github.com/HeyVincent-ai/cadence-mcp/issues)

## License

MIT
