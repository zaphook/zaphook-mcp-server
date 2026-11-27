# Claude (claude.ai and Claude Desktop)

Custom connectors added on claude.ai are also available in the Claude Desktop app with the same account.

1. Go to **Settings → Connectors** and click **Add custom connector**.
2. Fill in:
   - **Name:** `ZapHook`
   - **Remote MCP server URL:** `https://api.zaphook.cloud/mcp`
3. Under **Authentication**, select **No login**. Claude may pre-select "Log in now" because the server asks for credentials; the ZapHook server uses an API key header instead of OAuth.
4. Under **Request headers**, click **Add header**:
   - **Name:** `Authorization`
   - **Value:** `Bearer YOUR_API_KEY` (the word `Bearer`, one space, then your key)
5. Save. Open a new chat, enable the connector and ask: *"List my ZapHook channels."*

The header value is stored by Claude and is not shown again. To change the key, edit the connector.

## Notes

- claude.ai cannot read files from your computer, so media is sent by public HTTPS link. See [troubleshooting](../docs/troubleshooting.md#sending-media).
- Claude asks before running tools. Review each send: the recipient and the exact text.
