# Turso Cloud — Codex plugin

Manage [Turso Cloud](https://turso.tech) from Codex — databases, groups, and SQL —
through the hosted Turso MCP server (`mcp.turso.ai`) with OAuth login.

This plugin bundles the MCP server **and** the Turso skill (ecosystem guidance +
how to use the tools well), so Codex gets both the tools and the know-how.

## Install (marketplace)

```bash
codex plugin marketplace add tursodatabase/turso-mcp
codex plugin add turso@turso
```

Then authenticate:

```bash
codex mcp login turso
```

A browser opens to log in to Turso and approve access (pick an organization, and
optionally a single group + permissions for least-privilege).

## Just the MCP server (no skill)

If you only want the tools, add the server to `~/.codex/config.toml` instead:

```toml
[mcp_servers.turso]
url = "https://mcp.turso.ai/mcp"
```

then `codex mcp login turso`. Installing the plugin above is preferred — it also
delivers the skill.

## What you can do

List, create, branch (incl. point-in-time), configure, and delete databases; run
read / write / delete / schema SQL; and view per-database query analytics. Reads
and destructive operations are annotated so Codex can confirm before mutating.
