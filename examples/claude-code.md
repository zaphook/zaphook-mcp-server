# Claude Code

## Add the server

macOS / Linux:

```bash
export ZAPHOOK_API_KEY="YOUR_API_KEY"
claude mcp add --transport http zaphook https://api.zaphook.cloud/mcp \
  --scope user --header "Authorization: Bearer $ZAPHOOK_API_KEY"
```

Windows PowerShell:

```powershell
$env:ZAPHOOK_API_KEY = "YOUR_API_KEY"
claude mcp add --transport http zaphook https://api.zaphook.cloud/mcp --scope user --header "Authorization: Bearer $env:ZAPHOOK_API_KEY"
```

`--scope user` makes the server available in every folder. Without it, the server is only added to the current project.

## Check

```bash
claude mcp list
```

`zaphook` should show **Connected**. Inside a session, `/mcp` lists the tools.

Connected only means the server answered. A wrong key shows up on the first tool call, as "API key inválida, ausente ou revogada".

## Change the key

```bash
claude mcp remove zaphook -s user
```

Then add it again with the new key.

## Local files

Claude Code can run commands on your machine, so it can upload a local file with the ZapHook REST API (`POST /api/public/v1/media/upload`) and send it by media ID. See [troubleshooting](../docs/troubleshooting.md#sending-media).
