# Turso Cloud — Codex plugin

Manage [Turso Cloud](https://turso.tech) — organizations, databases, groups — and
run SQL, straight from Codex. This plugin points Codex at the hosted Turso MCP
server (`mcp.turso.ai`) and logs you in with OAuth: **no API token to copy, no
environment variable, no secret in the repo.**

## Install

```text
codex plugin marketplace add tursodatabase/turso-mcp
codex plugin add turso@turso
codex mcp login turso
```

`codex mcp login turso` opens your browser. Log in to Turso (if needed), land on
the **consent screen**, choose the **organization** (and optionally a single
**group** to scope the token to, with read-only / full-access / custom
permissions), and approve. Codex stores the token and refreshes it
automatically. You're connected.

## MCP-only alternative (no plugin)

Any MCP-capable Codex install can connect directly. Add this to
`~/.codex/config.toml` (or a trusted project's `.codex/config.toml`), then run
`codex mcp login turso`:

```toml
[mcp_servers.turso]
url = "https://mcp.turso.ai/mcp"
```

Self-hosting / BYOC / testing? Point `url` at your own deployment instead.

## What you can do

Ask Codex things like *"list my databases"*, *"create a database in the prod
group"*, *"branch app-db as of yesterday"*, *"what are the slowest queries on
app-db"*, or *"add an index to the users table"*. The server exposes tools to:

- **Databases** — list, inspect, create, delete, branch (incl. point-in-time),
  list branches, and update configuration (delete-protection, IP/VPC allow lists,
  size limit).
- **SQL** — run read-only queries, writes (INSERT/UPDATE), deletes, and schema
  changes (DDL), each with its own tool so the agent picks the least-powerful one.
  The write tool refuses DELETE/DROP — those have dedicated, clearly-named tools.
- **Insight** — per-database analytics (top queries by frequency/latency).

Read-only vs. destructive operations are annotated, so Codex can reason about
side effects and confirm before doing anything risky.

## How auth works (and why it's safe)

- **OAuth 2.1 + PKCE**, fully discoverable: pointed only at the server URL, Codex
  reads the `WWW-Authenticate` challenge → protected-resource metadata →
  authorization-server metadata to find every endpoint. There is nothing to
  paste.
- The **consent screen lives on the Turso dashboard** (the only place that can
  see your login session). It mints an **organization- or group-scoped** token.
- Every tool **forwards your token to the real Turso platform API**, which
  enforces org-binding, role, scope, and writes the audit record. The MCP layer
  makes no authorization decisions of its own and stores no privilege.

## What's in here

- `.mcp.json` — the MCP server definition (remote HTTP; OAuth is auto-discovered).
- `skills/turso/SKILL.md` — guidance for the agent on the Turso ecosystem
  (engine choice, drivers) and how to use the tools well.
- `.codex-plugin/` — the Codex plugin manifest.

## Links

- Turso Cloud: https://turso.tech
- Docs: https://docs.turso.tech
- Privacy policy: https://turso.tech/privacy-policy

## License

MIT — see [LICENSE](../LICENSE).
