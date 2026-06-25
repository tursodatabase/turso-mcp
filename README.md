# Turso for AI agents

Connect your AI coding agent to [Turso Cloud](https://turso.tech) — manage
organizations, databases, and groups, and run SQL — through the hosted Turso MCP
server (`mcp.turso.ai`), with **OAuth login (no API token to copy)**.

The MCP server is the same for every agent; this repo holds the per-agent
packaging. It also doubles as the plugin **marketplace** each agent's CLI can add.

## Agents

| Agent | Status | Location |
|-------|--------|----------|
| **Claude Code** | ✅ available | [`claude-code/`](claude-code/) |
| **Codex** | ✅ available | [`codex/`](codex/) |
| Cursor | 🛠️ planned | `cursor/` |

### Claude Code

```text
/plugin marketplace add tursodatabase/turso-mcp
/plugin install turso@turso
```

Then `/mcp` → **turso** → **Authenticate**. See [`claude-code/README.md`](claude-code/README.md)
for the full flow and details.

### Codex

```text
codex plugin marketplace add tursodatabase/turso-mcp
codex plugin add turso
codex mcp login turso
```

Bundles the MCP server **and** the Turso skill. See [`codex/README.md`](codex/README.md).
(MCP-only alternative: add `[mcp_servers.turso] url = "https://mcp.turso.ai/mcp"`
to `~/.codex/config.toml`, then `codex mcp login turso` — but that omits the skill.)

### Other agents

Any MCP-capable client can also connect directly to the server — point it at
`https://mcp.turso.ai/mcp` and complete the OAuth login.

## How it works

- One hosted MCP server, `https://mcp.turso.ai/mcp` (override with `TURSO_MCP_URL`).
- **OAuth 2.1 + PKCE**, fully discoverable from the server — nothing to paste.
- Consent (org / group / scopes) happens on the Turso dashboard, which mints a
  scoped token; every tool forwards it to the real Turso API, which enforces
  org-binding, role, scope, and audit. The MCP layer holds no privilege.

## Links

- Turso Cloud: https://turso.tech · Docs: https://docs.turso.tech
- Privacy policy: https://turso.tech/privacy-policy

## License

MIT — see [LICENSE](LICENSE).
