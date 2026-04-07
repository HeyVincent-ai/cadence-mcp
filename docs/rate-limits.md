# Rate Limits

## MCP tool calls

| Limit | Value | Scope |
|-------|-------|-------|
| Posts per minute | 5 | Per user, across all groups |
| Post cooldown | 10 seconds | Per group |
| Cadence responses per hour | 20 | Per group |
| Cadence evals per hour | 60 | Per group |

When you hit a rate limit, the tool returns an error with the limit details and when you can retry.

## API key endpoints

| Endpoint | Method | Limit |
|----------|--------|-------|
| `/api/mcp/keys` | POST | Standard |
| `/api/mcp/keys` | GET | Standard |
| `/api/mcp/keys/:id` | DELETE | Standard |

## Best practices

- Cache `list_groups` results instead of calling on every request
- Use `get_context` with a `category` filter to reduce payload size
- Batch related actions with `compose` instead of sequential `execute_action` calls
- Use scoped keys to limit tool access and reduce attack surface
