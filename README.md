# Ars Nova Claude Plugin

Internal marketplace of Claude skills (and, over time, MCP connectors) for the Ars Nova Singers team. One plugin everyone installs; new skills/tools are added here and pulled by the whole team.

## What's inside (v1.0.0)
- **season-pm** — the Project Manager / Dashboard Connector: pull from and feed the master Season Tracker (the data behind the WordPress Season Dashboard).
- **project-secretary** — a per-person, identity-aware proactive sweep (Kim / Tom / Jonathan / Zahnay) that triages your email, calendar, and meeting action items and feeds findings + tasks into the tracker.

## Install (each team member, once)
In the Claude desktop app:

- `/plugin marketplace add ArsNovaSingers/ars-nova-claude-plugin`
- `/plugin install ars-nova@ars-nova`

Then enable auto-update for this marketplace (Marketplaces tab). It is a public repo, so auto-update needs no credentials — enable it once and new skills show up automatically (or run `/plugin update` anytime).

### Cowork (cloud sessions)
Cowork sessions don't read local skills. To load this plugin in a Cowork project, add it to the project's `.claude/settings.json` enabledPlugins: `"ars-nova@ars-nova": true`.

## Requirements
Each person needs their own Ars Nova Google connector (authenticated as their own @arsnovasingers.org account) for the skills to reach the tracker and their inbox. The Project Secretary refuses to run under a non–Ars-Nova identity (fails safe).

## Adding a new skill or tool (author workflow)
1. Add a skill folder under `plugins/ars-nova/skills/<name>/SKILL.md` (or add an MCP server to `plugins/ars-nova/.mcp.json`).
2. Bump `version` in both `plugins/ars-nova/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json`.
3. Commit + push to `main`.
4. Team members with auto-update on get it on the next refresh; others run `/plugin update`.
