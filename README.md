# Turso Cloud — Claude Code plugin

Manage [Turso Cloud](https://turso.tech) — organizations, databases, groups — and
run SQL, straight from your AI agent. This plugin points Claude Code at the hosted
Turso MCP server (`mcp.turso.ai`) and logs you in with OAuth: **no API token to
copy, no environment variable, no secret in the repo.**

## Install

```text
/plugin marketplace add tursodatabase/turso-mcp-plugin
/plugin install turso@turso
```

Then connect:

1. Run `/mcp`, select **turso**, choose **Authenticate** — your browser opens.
2. Log in to Turso (if needed) and you'll land on a **consent screen**. Choose:
   - the **organization**,
   - optionally a single **group** to scope the token to (least privilege), or the
     whole organization for full access,
   - for a group, the **permissions** (read-only / full-access / custom).
3. Approve. Claude Code stores the token in your OS keychain and refreshes it
   automatically. You're connected.

To re-scope or switch orgs: `/mcp` → **turso** → **Clear authentication**, then
authenticate again.

## What you can do

Ask your agent things like *"list my databases"*, *"create a database in the
prod group"*, *"branch app-db as of yesterday"*, *"what are the slowest queries
on app-db"*, or *"add an index to the users table"*. The server exposes tools to:

- **Databases** — list, inspect, create, delete, branch (incl. point-in-time),
  list branches, and update configuration (delete-protection, IP/VPC allow lists,
  size limit).
- **SQL** — run read-only queries, writes (INSERT/UPDATE), deletes, and schema
  changes (DDL), each with its own tool so the agent picks the least-powerful one.
  The write tool refuses DELETE/DROP — those have dedicated, clearly-named tools.
- **Insight** — per-database analytics (top queries by frequency/latency).

Read-only vs. destructive operations are annotated, so Claude can reason about
side effects and confirm before doing anything risky.

## How auth works (and why it's safe)

- **OAuth 2.1 + PKCE**, fully discoverable: pointed only at the server URL, Claude
  Code reads the `WWW-Authenticate` challenge → protected-resource metadata →
  authorization-server metadata to find every endpoint. Hosted clients can also
  register via Dynamic Client Registration; Claude Code uses the shared public
  client id `turso-mcp` (a non-secret label — security is PKCE + the redirect +
  login, not id secrecy).
- The **consent screen lives on the Turso dashboard** (the only place that can see
  your login session). It mints an **organization- or group-scoped** token.
- Every tool **forwards your token to the real Turso platform API**, which enforces
  org-binding, role, scope, and writes the audit record. The MCP layer makes no
  authorization decisions of its own and stores no privilege.

## Configuration

The server URL is overridable (self-hosted / BYOC / testing) via `TURSO_MCP_URL`;
it defaults to `https://mcp.turso.ai/mcp`.

```jsonc
// .mcp.json
{
  "mcpServers": {
    "turso": {
      "type": "http",
      "url": "${TURSO_MCP_URL:-https://mcp.turso.ai/mcp}",
      "oauth": { "clientId": "turso-mcp" }
    }
  }
}
```

## What's in here

- `.mcp.json` — the MCP server definition (remote HTTP, OAuth).
- `skills/turso/SKILL.md` — guidance for the agent on the Turso ecosystem
  (engine choice, drivers) and how to use the tools well.
- `.claude-plugin/` — the plugin and marketplace manifests.

## Links

- Turso Cloud: https://turso.tech
- Docs: https://docs.turso.tech
- Privacy policy: https://turso.tech/privacy-policy

## License

MIT — see [LICENSE](LICENSE).
