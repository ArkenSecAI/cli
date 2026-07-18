# cli
ArkenSec CLI — open-source command-line interface for the ArkenSec autonomous pentesting platform

## Connect to the ArkenSec MCP server

The ArkenSec MCP (Model Context Protocol) server lets AI coding agents — Claude
Code, Cursor, VS Code Copilot, Windsurf, Cline, Continue, Zed — pull your live
ArkenSec security context (findings, scans, and compliance posture) into the IDE
as **read-only** tools.

- **Endpoint:** `https://mcp.arkensec.com/mcp` (Streamable HTTP)
- **Auth:** an ArkenSec API key sent as a bearer token —
  `Authorization: Bearer ask_live_...` (or `ask_test_...`). Create a key in the
  dashboard under **Settings → API Keys**
  ([app.arkensec.com/settings/api-keys](https://app.arkensec.com/settings/api-keys)).
  API keys require the Pro tier.
- **Scopes:** `findings:read`, `findings:write`, `scans:read`, `scans:write`,
  `compliance:read`, `reports:read`, `keys:read`, `keys:write`. The MCP server's
  tools are read-only; the write scopes are used by this CLI.

### Client config

**Claude Code** — run `claude mcp add`, or add to `~/.claude/mcp.json`:

```json
{
  "mcpServers": {
    "arkensec": {
      "url": "https://mcp.arkensec.com/mcp",
      "headers": { "Authorization": "Bearer ask_live_YOUR_KEY_HERE" }
    }
  }
}
```

**Cursor** — Settings → Features → MCP → add the same `mcpServers` JSON as above
(or use the one-click install on the dashboard's MCP setup page,
[app.arkensec.com/settings/developer/mcp](https://app.arkensec.com/settings/developer/mcp)).

**VS Code Copilot** — add the same `mcpServers` JSON to `~/.vscode/mcp.json`.

Windsurf, Cline, Continue, and Zed use the same endpoint and bearer header; see
the per-client snippets on the dashboard's MCP setup page.

### What you get

Eight read-only tools — `search_findings`, `get_finding`, `get_security_grade`,
`list_recent_scans`, `get_scan`, `list_findings_by_framework`,
`get_compliance_posture`, `list_affected_files`. For example, ask your agent
"what are my open critical findings?" and it will call `search_findings` and
return them, each with the exact `arkensec` CLI command to act on it. Write
actions (start a scan, mark a finding fixed) live in this CLI, which uses the
same API keys.

### Troubleshooting

| Symptom | Likely cause |
|---|---|
| `401` + `WWW-Authenticate: Bearer … oauth-protected-resource` | Missing / wrong / revoked API key |
| `403 insufficient_scope` in a tool result | Key created without that scope — regenerate with the scope enabled |
| Connection refused | Wrong endpoint URL, or the key isn't active yet |
