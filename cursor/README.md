# Turso Cloud — Cursor plugin

Manage [Turso Cloud](https://turso.tech) — organizations, databases, groups — and
run SQL, straight from Cursor. This plugin points Cursor at the hosted Turso MCP
server (`mcp.turso.ai`) and logs you in with OAuth: **no API token to copy, no
environment variable, no secret in the repo.**

## Install

**From the marketplace:** search for **Turso** in Cursor's plugin browser
(Customize → Plugins) or on [cursor.com/marketplace](https://cursor.com/marketplace),
and click install.

**One-click:** [Add to Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=turso&config=eyJ1cmwiOiJodHRwczovL21jcC50dXJzby5haS9tY3AifQ==)
(adds the MCP server without the plugin's skill).

**Manual fallback** — add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json`
(per-project):

```json
{
  "mcpServers": {
    "turso": { "url": "https://mcp.turso.ai/mcp" }
  }
}
```

Then connect: open **Settings → MCP**, select **turso**, and follow the login
prompt — your browser opens. Log in to Turso (if needed) and you'll land on a
**consent screen**. Choose:

- the **organization**,
- optionally a single **group** to scope the token to (least privilege), or the
  whole organization for full access,
- for a group, the **permissions** (read-only / full-access / custom).

Approve, and you're connected. To re-scope or switch orgs, disconnect the server
in Settings → MCP and authenticate again.

### Cursor web & cloud agents

Cloud (background) agents don't read this plugin's `mcp.json` — add the server
once for your account instead: at [cursor.com/agents](https://cursor.com/agents),
open the **“+” menu** next to the model picker → hover **MCP Servers** → **Add
MCP**, and enter `https://mcp.turso.ai/mcp`. The same OAuth consent flow applies.

## What you can do

Ask Cursor things like *"list my databases"*, *"create a database in the
prod group"*, *"branch app-db as of yesterday"*, *"what are the slowest queries
on app-db"*, or *"add an index to the users table"*. The server exposes tools to:

- **Databases** — list, inspect, create, delete, branch (incl. point-in-time),
  list branches, and update configuration (delete-protection, IP/VPC allow lists,
  size limit).
- **SQL** — run read-only queries, writes (INSERT/UPDATE), deletes, and schema
  changes (DDL), each with its own tool so the agent picks the least-powerful one.
  The write tool refuses DELETE/DROP — those have dedicated, clearly-named tools.
- **Insight** — per-database analytics (top queries by frequency/latency).

Read-only vs. destructive operations are annotated, so the agent can reason
about side effects and confirm before doing anything risky.

## How auth works (and why it's safe)

- **OAuth 2.1 + PKCE**, fully discoverable: pointed only at the server URL,
  Cursor reads the `WWW-Authenticate` challenge → protected-resource metadata →
  authorization-server metadata to find every endpoint, and registers itself via
  Dynamic Client Registration (all clients share the public id `turso-mcp` — a
  non-secret label; security is PKCE + the redirect + login, not id secrecy).
- The **consent screen lives on the Turso dashboard** (the only place that can see
  your login session). It mints an **organization- or group-scoped** token.
- Every tool **forwards your token to the real Turso platform API**, which enforces
  org-binding, role, scope, and writes the audit record. The MCP layer makes no
  authorization decisions of its own and stores no privilege.

## What's in here

- `mcp.json` — the MCP server definition (remote HTTP, OAuth).
- `skills/turso/SKILL.md` — guidance for the agent on the Turso ecosystem
  (engine choice, drivers) and how to use the tools well.
- `.cursor-plugin/plugin.json` — the plugin manifest.

## Links

- Turso Cloud: https://turso.tech
- Docs: https://docs.turso.tech
- Privacy policy: https://turso.tech/privacy-policy

## License

MIT — see [LICENSE](../LICENSE).
