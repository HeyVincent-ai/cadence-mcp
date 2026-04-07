# Quickstart

Get your AI agent talking to your Cadence groups in under 2 minutes.

## Prerequisites

- A Cadence account at [textcadence.xyz](https://textcadence.xyz/login)
- At least one group (create one in the app or add Cadence to an iMessage/Telegram group)

## Step 1: Create an API key

1. Sign in at [textcadence.xyz/login](https://textcadence.xyz/login)
2. Go to **Account > API Keys**
3. Click **Create Key**
4. Copy the key (starts with `cdk_`). You won't see it again.

## Step 2: Configure your agent

### Claude Desktop

Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "cadence": {
      "url": "https://textcadence.xyz/mcp/s/cdk_YOUR_API_KEY"
    }
  }
}
```

Restart Claude Desktop.

### Claude Code

Add to `.mcp.json` in your project root:

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

### Cursor

1. Open **Settings > MCP Servers**
2. Add a new server:
   - **Name**: Cadence
   - **URL**: `https://textcadence.xyz/mcp`
   - **Auth**: Bearer token, value `cdk_YOUR_API_KEY`

### Custom MCP client

Send JSON-RPC 2.0 requests to `https://textcadence.xyz/mcp`:

```bash
curl -X POST https://textcadence.xyz/mcp \
  -H "Authorization: Bearer cdk_YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "tools/list",
    "params": {},
    "id": 1
  }'
```

## Step 3: Verify it works

Ask your agent:

> "List my Cadence groups"

Your agent calls `list_groups` and returns your groups with IDs, names, and member counts.

Then try:

> "What's happening in my [group name] group?"

Your agent calls `get_context` and returns the group's extracted knowledge: decisions, action items, plans, and recent messages.

## Step 4: Post a message

> "Post 'Hey everyone, standup at 3pm today' to my Dev Team group"

Your agent calls `post`, Cadence synthesizes the message to fit the group's tone, and delivers it to the chat (iMessage, Telegram, or web).

## Step 5: Use shared integrations

If group members have connected integrations (GitHub, Canva, Strava, etc.), your agent can use them:

> "What integrations are available in my Dev Team group?"

Your agent calls `what_can_we_do` and lists every service and action available.

> "List the open GitHub issues in the Dev Team group"

Your agent calls the dynamically-registered `list_issues` tool (available because someone in the group shared their GitHub access).

## What's next

- **[Tools Reference](tools.md)** — See every tool your agent can call
- **[Auth](auth.md)** — Scope your API key to specific tools
- **[Recipes](recipes.md)** — Apply behavior templates to groups
- **[Authority Model](authority.md)** — Understand how permissions work
