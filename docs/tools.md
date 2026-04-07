# Tools Reference

Every tool available through the Cadence MCP server. Your agent discovers these automatically via `tools/list`.

## Group Tools

### `list_groups`

List your Cadence groups.

**Parameters:** None

**Returns:** Array of groups with `id`, `name`, `channel` (imessage/telegram/web), `memberCount`, `archived`.

**Example response:**
```json
[
  {
    "id": "grp_abc123",
    "name": "Dev Team",
    "channel": "telegram",
    "memberCount": 4,
    "archived": false
  }
]
```

---

### `get_context`

Read what Cadence knows about a group: extracted knowledge (decisions, plans, action items, facts), members, and recent messages.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID from `list_groups` |
| `category` | enum | No | Filter: `all`, `decision`, `plan`, `recommendation`, `fact`, `action_item`, `preference`, `question` |

**Returns:** Group metadata, member list, knowledge entries (with category, content, source, confidence), and the 20 most recent messages.

---

### `post`

Post a message to a group on your behalf. Cadence reads the room and synthesizes the message to fit naturally.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID |
| `message` | string | Yes | What you want to say (1-4000 chars) |

**Returns:** Delivery status, final message text (may be adjusted for tone), whether synthesis occurred.

**Rate limits:** 5 posts/minute per user, 10s cooldown per group.

---

## Service Tools

### `list_services`

List integrations connected to a group. Only shows services where a member has explicitly shared access.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID |

**Returns:** Array of connected services with capability type and provider name.

---

### `list_actions`

List available actions for a connected service in a group. Shows the full menu and which actions are granted.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID |
| `capability_type` | string | Yes | Service type (e.g., `strava`, `github`, `gmail`) |

**Returns:** Array of actions with slug, name, description, and granted status.

---

### `execute_action`

Execute an action using a connected service. Granted actions execute immediately. Ungranted actions trigger an approval request via DM to the service owner.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group where the action runs |
| `capability_type` | string | Yes | Service type |
| `action` | string | Yes | Action slug |
| `description` | string | Yes | Human-readable description of what and why |
| `details` | object | No | Action-specific parameters |

**Returns:** Success/failure, response data (filtered per grant rule), resource reference if applicable, request ID if approval needed.

---

### `what_can_we_do`

List all connected services and granted capabilities available in a group. Human-readable summary.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID |

---

## Orchestration Tools

### `compose`

Execute a multi-step plan using different connected services in sequence. Each step can reference results from prior steps.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group where all actions run |
| `plan` | string | Yes | One-sentence description of the composition |
| `steps` | array | Yes | 1-10 ordered steps, each with `capability_type`, `action`, `description`, and optional `details` |

**Returns:** Combined result with per-step status, data, errors, and approval request IDs.

**Example:**
```json
{
  "group_id": "grp_abc123",
  "plan": "Create a design and post it for review",
  "steps": [
    {
      "capability_type": "canva",
      "action": "create_design",
      "description": "Create an Instagram story for the product launch",
      "details": {"type": "instagram_story"}
    },
    {
      "capability_type": "slack",
      "action": "send_message",
      "description": "Share the design link in #design-review",
      "details": {"channel": "#design-review"}
    }
  ]
}
```

---

## Recipe Tools

### `list_recipes`

List all available recipes, both system templates and user-created.

**Parameters:** None

---

### `get_recipe`

Get full details of a recipe including its goal and instructions.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `recipe` | string | Yes | Recipe slug or ID |

---

### `create_recipe`

Create a custom recipe that defines how a group should operate.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `name` | string | Yes | Recipe name (1-100 chars) |
| `goal` | string | Yes | What the group is for and how the agent should behave (1-2000 chars) |
| `instructions` | string | No | Markdown behavioral rules that become part of the system prompt |
| `description` | string | No | One-line summary for browsing |

---

### `apply_recipe`

Apply a recipe to a group. The recipe's instructions shape how Cadence behaves in that group.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID |
| `recipe` | string | Yes | Recipe slug or ID |

---

## Resource Tools

### `list_my_resources`

List resources created, modified, or pinned in a group (designs, issues, documents, etc).

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID |
| `provider` | string | No | Filter by provider (e.g., `composio_canva`) |
| `resource_type` | string | No | Filter by type (e.g., `design`, `issue`, `pull_request`) |

---

### `pin_resource`

Pin an external resource to a group's resource memory.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `group_id` | string | Yes | Group ID |
| `provider` | string | Yes | Service (e.g., `composio_github`) |
| `resource_type` | string | Yes | Type (e.g., `issue`, `design`) |
| `external_id` | string | No | Resource ID from provider |
| `external_url` | string | No | URL to resource |
| `title` | string | No | Human-readable title |

Either `external_id` or `external_url` is required.

---

## Network Tools

### `propose`

Post an ask or offer to the Cadence Global network for matching.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `type` | enum | Yes | `ask` or `offer` |
| `description` | string | Yes | What you want or can provide (1-2000 chars) |

---

### `review`

Review pending intro requests involving you.

**Parameters:** None

**Returns:** Pending connections with direction, status, and masked counterparty info.

---

### `accept`

Accept a pending intro request and create a shared thread.

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `connection_id` | string | Yes | Connection ID from `review` |

---

## Dynamic Tools

When group members grant access to integrations, those actions are automatically registered as individual MCP tools. For example, if someone shares Strava read access, your agent gets a `get_activities` tool.

Dynamic tools always take:
- `group_id` (string) — which group to use the integration in
- `details` (object, optional) — action-specific parameters

Dynamic tools are checked against live grant rules on every call. If a grant is revoked, the tool stops working immediately.
