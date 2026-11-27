# Client examples

Replace `YOUR_API_KEY` with a key from the ZapHook dashboard ([how to create one](../docs/authentication.md)). Never commit a real key.

| File | Client | Where it goes |
|---|---|---|
| [claude-ai.md](claude-ai.md) | claude.ai and Claude Desktop | Settings → Connectors → Add custom connector |
| [claude-code.md](claude-code.md) | Claude Code | `claude mcp add` command |
| [cursor.json](cursor.json) | Cursor | `~/.cursor/mcp.json` or `.cursor/mcp.json` |
| [vscode.json](vscode.json) | VS Code (GitHub Copilot) | `.vscode/mcp.json`; VS Code asks for the key and stores it securely |
| [kiro.json](kiro.json) | Kiro | `~/.kiro/settings/mcp.json` or `.kiro/settings/mcp.json`; set `ZAPHOOK_API_KEY` in your environment |
| [mcp-remote.json](mcp-remote.json) | Any client that only supports local (stdio) servers | The client's MCP config. Requires Node.js. `Authorization:${AUTH_HEADER}` has no space after the colon on purpose, to avoid argument-splitting issues on Windows. |
| [prompts.md](prompts.md) | Any | Example requests |
