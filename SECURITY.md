# Security policy

## Reporting a vulnerability

Please **do not open a public issue** for security problems.

Report them privately through GitHub: open the [Security tab](https://github.com/zaphook/zaphook-mcp-server/security) of this repository and click **Report a vulnerability**. Include what you found, how to reproduce it and the impact you expect. Do not include real API keys, phone numbers or message content.

We will acknowledge the report, keep you updated while we investigate, and credit you in the fix notes if you want.

## Scope

- The hosted server at `https://api.zaphook.cloud/mcp`.
- The documentation and examples in this repository, for example an example that could lead users to expose a key.

Out of scope: denial-of-service testing and social engineering. Test only against your own ZapHook account and your own WhatsApp numbers.

## If your API key leaked

Delete it right away in the ZapHook dashboard (**Settings → API Keys**) and create a new one. A deleted key stops working immediately.
