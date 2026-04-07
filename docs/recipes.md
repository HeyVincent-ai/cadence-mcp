# Recipes

A recipe is a prompt that tells Cadence how to run a group. What to track, when to remind, how to sound.

## Browsing recipes

Your agent can list all available recipes:

```
> "Show me the available Cadence recipes"
```

This calls `list_recipes` and returns system templates and any custom recipes you've created.

## Getting recipe details

```
> "Tell me about the Dev Team recipe"
```

This calls `get_recipe` with the recipe slug and returns the full goal, instructions, and required services.

## Applying a recipe to a group

```
> "Apply the Content Swarm recipe to my Launch Crew group"
```

This calls `apply_recipe`. The recipe's instructions become part of how Cadence behaves in that group.

## Creating a custom recipe

```
> "Create a recipe called 'Weekly Standup' that tracks what everyone committed to and follows up on Mondays"
```

This calls `create_recipe` with:
- **name**: The recipe name
- **goal**: What the group is for and how the agent should behave
- **instructions** (optional): Detailed behavioral rules in markdown
- **description** (optional): One-line summary

### Example: Custom recipe via API

```json
{
  "name": "Weekly Standup",
  "goal": "Track team commitments and hold people accountable. Every Monday, remind the group what each person said they'd do and ask for updates.",
  "instructions": "## Tracking\n- Extract commitments from messages (\"I'll do X by Y\")\n- Store who committed to what and when\n\n## Monday Check-in\n- Post a summary of last week's commitments\n- Tag people who haven't reported back\n- Keep it friendly but direct",
  "description": "Weekly commitment tracking with Monday follow-ups"
}
```

## System recipes

These are built-in recipes available to all users:

| Recipe | Description | Services |
|--------|-------------|----------|
| Content Swarm | Create and ship marketing content | Canva, Gmail, Notion |
| Dev Team | Ship code with tracked issues | GitHub, Linear, Slack |
| DevOps Escalation | Incident response and postmortems | GitHub, Slack, Calendar |
| Supply Chain | Order tracking and logistics | Linear, Slack, Gmail |
| Sales Pipeline | Deal flow with shared visibility | Gmail, Calendar, Notion |
| Study Group | Deadlines, notes, and study sessions | Calendar, Notion |
| Proof or It Didn't Happen | Wager with group verification | USD Wallet |
| Strava Fitness Challenge | Compete on real workout data | Strava, USD Wallet |
| Tarot | Daily card pull | None |
| Anonymous Confessions | Anonymous message drops | None |
