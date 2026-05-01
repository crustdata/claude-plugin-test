# Crustdata Skills (Claude Code Plugin Marketplace)

Open-source Claude Code skills for working with Crustdata's B2B data tools. Used by both the Crustdata team (via [internal.crustdata.com/skills](https://internal.crustdata.com/skills)) and customers (via the public marketplace).

This directory is the development copy — the canonical source lives at https://github.com/crustdata/skills. After pushing to GitHub, anyone can install with `/plugin marketplace add crustdata/skills`.

## What's here

- `.claude-plugin/marketplace.json` — the marketplace registry (lets `/plugin marketplace add crustdata/skills` resolve)
- `crustdata-research/` — one plugin with the research skills
  - `skills/research-person/SKILL.md` — research a LinkedIn profile
  - `skills/research-company/SKILL.md` — research a company

The skills call Crustdata MCP tools (`crustdata_people_enrich`, etc.) but the plugin does NOT bundle its own MCP server — users are expected to have the Crustdata Connector enabled in claude.ai → Settings → Connectors, which makes those tools available in Claude Code automatically.

This sits alongside the existing `candidate-sourcing/` and `email-enrichment/` bare-skill directories in the public repo. Both distribution methods coexist:
- **Plugin marketplace** (this directory) — install via `/plugin marketplace add crustdata/skills`
- **Bare skill clone** (existing pattern) — `git clone` the repo, copy individual skill dirs to `~/.claude/skills/`

## How users install (one time)

In Claude Code, run:

```
/plugin marketplace add crustdata/skills
/plugin install crustdata-research@crustdata-skills
```

Choose **user scope** when prompted (so the plugin is available across all your Claude Code sessions, not just one repo).

After install, make sure the Crustdata Connector is enabled in claude.ai → Settings → Connectors. The skills will use whatever Crustdata MCP tools that connector exposes. No env vars or token setup required.

## How users invoke a skill

Once installed, users can either:
- Type `/research-person https://linkedin.com/in/foo` directly in Claude Code, or
- Click a "Run in Claude Code" button in the internal dashboard, which opens a `claude://code/new?prompt=...` deeplink prefilled with the skill invocation.

## Adding a new skill

1. Create `plugins/crustdata-research/skills/<skill-name>/SKILL.md` with frontmatter (`name`, `description`).
2. Bump the plugin version in `plugins/crustdata-research/.claude-plugin/plugin.json`.
3. Push. Users get the new version via `/plugin update` (or auto-update if enabled).
4. Add a corresponding card to `src/app/skills/page.tsx` in the dashboard so users can launch it with one click.
