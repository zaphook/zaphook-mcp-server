# Troubleshooting

## Connection

### The client cannot connect

- The URL must be exactly `https://api.zaphook.cloud/mcp`: HTTPS, no trailing parameters.
- The transport must be **Streamable HTTP** (sometimes labeled "HTTP"). SSE is not supported.
- The header must be `Authorization: Bearer YOUR_API_KEY`. See [authentication.md](authentication.md).

### claude.ai says the server requires login

When you add the connector, claude.ai probes the server, sees that it needs credentials and pre-selects "Log in now". Choose **No login** and add the `Authorization` header under **Request headers**. The server uses API keys, not OAuth.

### The tools do not show up after a change

Most clients load the tool list when they start. Restart the client or reconnect the server (in Claude Code: `/mcp`).

## Sending messages

### "Message not delivered" or a Meta error outside the 24-hour window

WhatsApp only allows free-form messages (text, media, buttons, lists) within **24 hours of the contact's last message**. Outside that window, only approved **templates** are accepted.

1. Check the window: `zaphook_get_conversation` returns `customer_window_open` and `customer_window_expires_at`.
2. If it is closed, list templates with `zaphook_list_channel_templates` and send one with `zaphook_send_template`.

### Template not found or rejected

- Use the exact `name` and `language` (for example `pt_BR`) returned by `zaphook_list_channel_templates`.
- Only templates with status `APPROVED` can be sent.
- The number of parameters in `components` must match the template variables.

### Phone number rejected

`to` must be digits only, with the country code: `5511999998888`. No `+`, spaces, dashes or parentheses.

### A send timed out

The message may have been sent anyway. Before retrying, check `zaphook_list_events` or the conversation history, so the contact does not receive it twice. The server never retries a send on its own.

## Sending media

Media tools accept either:

- `{ "link": "https://..." }`: a **public HTTPS URL** that opens the file directly, without login or redirects to an HTML page; or
- `{ "id": "MEDIA_ID" }`: the ID of media already uploaded to WhatsApp.

Files on your computer cannot be passed to the MCP server directly: the server runs remotely and cannot read your disk, and pasting a file into a tool call as text is unreliable. Options:

1. Host the file at a public HTTPS URL (your website, a storage bucket, a CDN) and send the link. Anyone with the link can open it, so do not use this for sensitive files.
2. Upload the file with the ZapHook REST API (`POST https://api.zaphook.cloud/api/public/v1/media/upload`, multipart fields `channel_id`, `type`, `file`, with your API key) and send the returned `id`. Assistants with terminal access, like Claude Code or Cursor, can run this upload for you. See the [ZapHook API documentation](https://zaphook.gitbook.io/docs).

Never share your Meta access token to work around this. ZapHook keeps it for you.

Format tips: images as JPG or PNG; stickers as .webp 512x512 px. SVG is not accepted as an image; send it as a document.

## Reading data

### Inbox tools return "inbox not enabled"

The WhatsApp inbox is not enabled for your account. Contact ZapHook support.

### Events or conversations look empty

- Check the filters: `channel_id`, dates and `channel_type` (`all`, `standard`, `coexistence`).
- Make sure the number has received messages since the channel was connected.
- Old events may have expired under your plan's log retention.

### Many events with `failed` delivery

The channel received the event, but your destination (webhook) rejected it. Open one with `zaphook_get_event` to see the destination's response. A common cause is a field mapping that expects message fields (like the text or the sender's phone) on events that do not have them, such as delivery status updates.

## Limits

- Requests are rate-limited per API key. On HTTP 429, wait for the time in the `Retry-After` header.
- Large results are truncated to keep responses readable; use filters and pagination.

## Still stuck?

Open an [issue](https://github.com/zaphook/zaphook-mcp-server/issues) with your client name and version, the tool you called and the error text. Remove API keys, phone numbers and message content first.
