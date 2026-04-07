# Authentication

## API Keys

All MCP requests require an API key. Keys start with `cdk_` and are 52 characters long.

### Creating a key

**Via the dashboard:**

1. Sign in at [textcadence.xyz/login](https://textcadence.xyz/login)
2. Go to **Account > API Keys**
3. Click **Create Key**, give it a label
4. Copy the key immediately. It's shown once.

**Via the API:**

```bash
curl -X POST https://textcadence.xyz/api/mcp/keys \
  -H "Authorization: Bearer YOUR_SESSION_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"label": "Claude Desktop"}'
```

Response:
```json
{
  "id": "key_abc123",
  "label": "Claude Desktop",
  "key": "cdk_a1b2c3d4e5f6...",
  "keyPrefix": "cdk_a1b2c3d4",
  "scopes": null,
  "createdAt": "2026-04-06T12:00:00Z",
  "expiresAt": null
}
```

### Using a key

Two methods, both equivalent:

**Bearer token** (recommended for programmatic use):
```
POST https://textcadence.xyz/mcp
Authorization: Bearer cdk_YOUR_KEY
Content-Type: application/json
```

**URL-embedded** (convenient for client config):
```
POST https://textcadence.xyz/mcp/s/cdk_YOUR_KEY
Content-Type: application/json
```

The URL method is useful for MCP clients that don't support custom headers (like some Claude Desktop configurations).

## Scoped Keys

By default, keys have access to all tools. You can restrict a key to specific tools using scopes.

### Scope types

| Pattern | Matches | Example |
|---------|---------|---------|
| Exact | One specific tool | `{"tool": "list_groups"}` |
| Wildcard | All tools matching prefix | `{"tool": "github_*"}` |
| All | Every tool | `{"tool": "*"}` |

### Creating a scoped key

```bash
curl -X POST https://textcadence.xyz/api/mcp/keys \
  -H "Authorization: Bearer YOUR_SESSION_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "label": "Strava Reader",
    "scopes": [
      {"tool": "list_groups"},
      {"tool": "get_context"},
      {"tool": "get_activities"},
      {"tool": "get_athlete_stats"}
    ]
  }'
```

A scoped key can only call the tools listed. Calls to other tools return an authorization error.

### Expiring keys

```bash
curl -X POST https://textcadence.xyz/api/mcp/keys \
  -H "Authorization: Bearer YOUR_SESSION_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "label": "Temporary",
    "expiresAt": "2026-05-01T00:00:00Z"
  }'
```

### Managing keys

**List all keys:**
```bash
curl https://textcadence.xyz/api/mcp/keys \
  -H "Authorization: Bearer YOUR_SESSION_TOKEN"
```

**Revoke a key:**
```bash
curl -X DELETE https://textcadence.xyz/api/mcp/keys/KEY_ID \
  -H "Authorization: Bearer YOUR_SESSION_TOKEN"
```

## Security

- Keys are hashed (SHA-256) before storage. Cadence never stores the raw key.
- Only the first 8 characters (`keyPrefix`) are stored in plaintext for identification.
- `lastUsedAt` is updated on each request so you can spot unused keys.
- Revoked and expired keys are rejected immediately.
