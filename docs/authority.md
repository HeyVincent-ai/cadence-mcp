# Authority Model

Cadence uses a permission system called the Authority Model to control which integrations your agent can use in each group. No agent can access a service unless the owner explicitly grants it.

## How it works

1. **People connect integrations** — Each person links their own GitHub, Canva, Gmail, etc. to their Cadence account.
2. **People share access with groups** — In the dashboard, you choose which groups can use your integrations and at what level.
3. **Grants define the scope** — Each grant specifies which actions are allowed, what parameters are locked, and how much of the response is visible.
4. **Agents operate within grants** — When your agent calls `execute_action`, Cadence checks the live grant rules before executing.

## Grant levels

Grants are organized into bundles by category:

| Category | Description | Example |
|----------|-------------|---------|
| **Read** | View and list data | List GitHub repos, view Strava activities |
| **Collaborate** | Create and contribute | Create GitHub issues, post Slack messages |
| **Manage** | Modify and configure | Update repo settings, manage calendar events |
| **Admin** | Full control | Delete resources, manage permissions |

Each service defines its own bundles. When a user grants the "Collaborate" bundle for GitHub, every action in that bundle (create issue, create PR, post comment) becomes available to the group.

## What happens without a grant

If your agent tries to execute an action that isn't granted, Cadence returns an error telling the user to visit the dashboard to configure permissions. No action is taken.

```json
{
  "success": false,
  "error": "This action requires a grant. Visit the dashboard to configure permissions."
}
```

## Parameter locks

Grant owners can lock specific parameters. For example, a GitHub grant might lock the repository to `my-org/my-repo`, so the agent can only interact with that specific repo.

Locked parameters are enforced server-side. The agent cannot override them.

## Response filtering

Each grant specifies a response mode:

| Mode | What the agent sees |
|------|-------------------|
| `full` | Complete response from the service |
| `summary` | AI-generated summary of the response |
| `snippet` | First portion of the response only |

This prevents data leakage. A Strava grant with `summary_only` response mode means the agent sees "User ran 5K in 24:30" but not the full GPS data, heart rate, or other details.

## Dynamic tool registration

When grants exist, Cadence automatically registers the granted actions as individual MCP tools. Your agent sees tools like `get_activities`, `list_repositories`, `create_design` alongside the standard tools.

These dynamic tools are checked against live grant rules on every call. Revoking a grant immediately removes the tool.

## Checking available permissions

Use `what_can_we_do` to see everything your agent can do in a group:

```
> "What can we do in the Dev Team group?"
```

This returns a human-readable summary of every connected service and granted action.
