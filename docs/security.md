# Security & Trust

Cadence is designed so you never lose control of your data or your integrations. Every action goes through permission checks, every result is filtered, and every operation is logged.

## You control access

Every integration you connect (GitHub, Gmail, Canva, Strava, etc.) stays under your control. You decide which groups can use your integrations and at what level:

| Level | What it allows | Example |
|-------|---------------|---------|
| **Read** | View and list data | List repos, view activities |
| **Collaborate** | Create and contribute | Open issues, send messages |
| **Manage** | Modify and configure | Update settings, manage events |
| **Admin** | Full control | Delete resources, manage permissions |

You can grant different levels to different groups. A running club gets Read access to your Strava. Your dev team gets Collaborate on GitHub. You choose.

## Scoped by default

Grants are not all-or-nothing. Every grant is scoped to specific actions within a service.

**Parameter locks** let you restrict exactly what an agent can touch. For example, you can grant GitHub access but lock it to a single repository. The agent cannot override locked parameters. This is enforced server-side.

**Response filtering** controls what the agent sees in return:

| Mode | What the agent sees |
|------|-------------------|
| `full` | Complete response |
| `summary` | AI-generated summary only |
| `snippet` | First portion only |

A Strava grant with `summary` mode means the agent sees "User ran 5K in 24:30" but not your GPS data, heart rate, or route.

## Writes need approval

For sensitive actions (spending money, sending messages, modifying data), grant owners can require explicit approval. When an agent tries to execute an action that requires approval, Cadence pauses and asks you first. Nothing happens until you say yes.

Actions without a grant are blocked entirely. Cadence returns an error directing the user to the dashboard. No fallback, no workaround.

## Transparent by design

Every action executed through Cadence is logged with:

- **What** ran (the specific action and parameters)
- **Who** triggered it (which agent, in which group)
- **When** it happened
- **What** was returned (filtered according to the grant's response mode)

You can review this history in the dashboard at any time.

## Revoking access

Revoking a grant takes effect immediately. The agent loses access to the integration's tools on the next request. There is no grace period, no cached permissions.

## API key security

If you connect via the MCP server, your API key is scoped to specific tools. A key scoped to `github_*` can only call GitHub-related tools. A key scoped to `get_groups` can only list groups. See [API key scoping](./auth.md) for details.

## What we don't do

- We don't store your integration credentials. OAuth tokens are managed by Composio and refreshed on demand.
- We don't auto-execute write actions without a grant. No grant means no access.
- We don't share your connected services with other users. Your integrations are only visible to groups you explicitly grant.
- We don't retain raw integration responses beyond the current request unless the grant allows it.

## Questions?

Open an issue on this repo or reach out at [cadence.bot](https://cadence.bot).
