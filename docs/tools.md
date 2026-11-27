# Tool reference

Every tool of the ZapHook MCP Server, with its parameters. The server publishes the same information through `tools/list`, so your client always sees the current schema.

- **📤** sends a real WhatsApp message to the contact. It cannot be undone.
- **🗑️** deletes data permanently.
- Phone numbers (`to`) are digits only, country code first, 10 to 15 digits. Example: `5511999998888`.
- IDs (`channel_id`, `dest_id`, `event_id`, `conversation_id`) are UUIDs returned by the list tools.
- Tool descriptions are written in Portuguese (Brazil); assistants understand them in any language.

## Contents

- [Inbox (read-only)](#inbox-read-only)
- [Messages](#messages)
- [Channels and templates](#channels-and-templates)
- [Destinations (webhooks)](#destinations-webhooks)
- [Events (delivery debugging)](#events-delivery-debugging)

## Inbox (read-only)

### `zaphook_list_conversations`

Lists WhatsApp inbox conversations, newest first. Each item has the contact (`contact.phone`, `contact.name`), `unread_count`, `channel_id`, `channel_type`, `last_message` (direction, type and a `preview` of up to 100 characters, text only; `null` when content is masked) and the 24-hour window (`customer_window_open`, `customer_window_expires_at`).

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `limit` | integer | no | min 1, max 100 |
| `offset` | integer | no | min 0 |
| `channel_id` | UUID | no |  |
| `channel_type` | `all` \| `standard` \| `coexistence` | no |  |
| `status` | `all` \| `active` \| `ended` | no |  |
| `unread` | boolean | no |  |
| `search` | string | no | min length 1, max length 100 |
| `updated_after` | ISO 8601 date-time | no |  |
| `updated_before` | ISO 8601 date-time | no |  |

### `zaphook_get_conversation`

Returns one conversation, in the same shape as a list item. To reply, use `zaphook_send_text` when `customer_window_open` is `true`, otherwise `zaphook_send_template`.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `conversation_id` | UUID | yes |  |

### `zaphook_list_conversation_messages`

Message history of a conversation, incoming and outgoing. Default order is newest first (`order=desc`); use `order=asc` for chronological order. When `has_more` is `true`, call again with `cursor` set to `next_cursor` and the same `order`.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `conversation_id` | UUID | yes |  |
| `limit` | integer | no | min 1, max 100 |
| `order` | `asc` \| `desc` | no |  |
| `cursor` | string | no | min length 1, max length 2000 |

## Messages

### `zaphook_send_text`

📤 Sends a text message. `text` is `{ "body": "...", "preview_url": false }` (body up to 4096 characters).

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `text` | object | yes |  |

### `zaphook_send_template`

📤 Sends an approved template; the way to start a conversation or write outside the 24-hour window. `template` is `{ "name", "language", "components" }`. List templates first to get the exact name, language (for example `pt_BR`) and variables. Example body parameters: `[{"type":"body","parameters":[{"type":"text","text":"Maria"}]}]`.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `template` | object | yes |  |

### `zaphook_send_image`

📤 Sends an image. `image` is `{ "link": "https://..." }` or `{ "id": "MEDIA_ID" }`.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `image` | object | yes |  |
| `caption` | string | no | max length 1024 |

### `zaphook_send_video`

📤 Sends a video. Same `link` / `id` object.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `video` | object | yes |  |
| `caption` | string | no | max length 1024 |

### `zaphook_send_audio`

📤 Sends an audio file. Same `link` / `id` object.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `audio` | object | yes |  |

### `zaphook_send_document`

📤 Sends a document (PDF, spreadsheet, etc.). Same `link` / `id` object, plus optional `filename` and `caption`.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `document` | object | yes |  |
| `caption` | string | no | max length 1024 |
| `filename` | string | no | max length 255 |

### `zaphook_send_sticker`

📤 Sends a sticker (.webp, 512x512 px). Same `link` / `id` object.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `sticker` | object | yes |  |

### `zaphook_send_location`

📤 Sends a location. `location` is `{ "latitude", "longitude", "name"?, "address"? }`.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `location` | object | yes |  |

### `zaphook_request_location`

📤 Sends a message asking the contact to share their location.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `body` | string | yes | min length 1, max length 1024 |

### `zaphook_send_contact`

📤 Sends one or more contact cards. Each contact has `name.formatted_name` and optional phones, emails, URLs, addresses, organization and birthday.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `contacts` | array of object | yes |  |

### `zaphook_send_buttons`

📤 Interactive message with 1 to 3 quick-reply buttons (`{ "id", "title" }`, title up to 20 characters).

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `body` | string | yes | min length 1, max length 1024 |
| `buttons` | array of object | yes |  |
| `header` | string | no | max length 60 |
| `footer` | string | no | max length 60 |

### `zaphook_send_list`

📤 Interactive list menu: up to 10 rows across all sections; section and row titles up to 24 characters.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `body` | string | yes | min length 1, max length 1024 |
| `button_text` | string | yes | min length 1, max length 20 |
| `sections` | array of object | yes |  |
| `header` | string | no | max length 60 |
| `footer` | string | no | max length 60 |

### `zaphook_send_reaction`

📤 Reacts to a message with an emoji; an empty `emoji` removes the reaction. `message_id` is the WhatsApp message ID (wamid) of the target message.

*Behavior: sends a real message.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `to` | string | yes | min length 10, max length 15, pattern `^\d+$` |
| `message_id` | string | yes | min length 1 |
| `emoji` | string | yes |  |

### `zaphook_mark_as_read`

Marks a received message as read (blue ticks). Sends no content. `message_id` is the wamid of a received message.

*Behavior: write, idempotent.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `message_id` | string | yes | min length 1 |

### `zaphook_send_typing`

Shows the "typing…" indicator for about 25 seconds and marks the message as read. Sends no content.

*Behavior: write, idempotent.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `message_id` | string | yes | min length 1 |

## Channels and templates

### `zaphook_list_channels`

Lists WhatsApp channels (standard and coexistence) with pagination. Use it first to find the `channel_id` other tools need. Secret fields come back as `***`.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `page` | integer | no | min 1 |
| `per_page` | integer | no | min 1, max 100 |
| `type` | `all` \| `standard` \| `coexistence` | no |  |
| `is_active` | boolean | no |  |
| `name` | string | no | max length 255 |
| `sort` | `created_at` \| `updated_at` \| `name` | no |  |
| `order` | `asc` \| `desc` | no |  |

### `zaphook_get_channel`

Returns one channel by `channel_id`, standard or coexistence.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |

### `zaphook_get_channel_health`

Asks Meta in real time for the number's health: connection status, `quality_rating`, `messaging_limit_tier`, verified name and number. May take a few seconds; fails if the channel is inactive or has no phone number ID.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |

### `zaphook_list_channel_templates`

Lists the channel's WhatsApp message templates (all statuses) from Meta, with `status`, `rejected_reason`, `components` and a `summary` of variables. Cursor pagination: repeat with `after` set to `data.pagination.next_cursor` while `has_next` is `true`.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `status` | string | no |  |
| `category` | string | no |  |
| `language` | string | no |  |
| `name` | string | no |  |
| `limit` | integer | no | min 1, max 100 |
| `after` | string | no |  |

### `zaphook_create_channel`

Creates a standard channel with an auto-generated webhook URL. The WhatsApp access token is configured in the ZapHook dashboard, not here. Coexistence channels are created from the dashboard's setup link.

*Behavior: write.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `name` | string | yes | min length 1, max length 255 |
| `phone_number_id` | string | no | min length 1, max length 255 |
| `waba_id` | string | no | min length 1, max length 255 |
| `verify_token` | string | no | min length 1, max length 255 |
| `webhook_secret` | string | no | min length 1, max length 255 |

### `zaphook_update_channel`

Updates `name`, `is_active` or `webhook_secret` of a standard channel. Send only what changes.

*Behavior: write, idempotent.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `name` | string | no | min length 1, max length 255 |
| `is_active` | boolean | no |  |
| `webhook_secret` | string | no | min length 1, max length 255 |

### `zaphook_delete_channel`

🗑️ Permanently deletes a standard channel, its destinations and its event logs. To pause instead, use `zaphook_update_channel` with `is_active=false`.

*Behavior: destructive.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |

## Destinations (webhooks)

### `zaphook_list_destinations`

Lists the destinations (URLs that receive events) of a channel, standard or coexistence. `headers` and `auth_config` values are masked.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |

### `zaphook_create_destination`

Creates a destination on a standard channel; events start being delivered to `endpoint_url`. Coexistence destinations are managed in the dashboard.

*Behavior: write.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `name` | string | yes | min length 1, max length 255 |
| `endpoint_url` | string | yes | pattern `^https?://` |
| `http_method` | string | no |  |
| `description` | string | no |  |
| `headers` | object | no |  |
| `field_mapping` | object | no |  |
| `auth_config` | object | no |  |
| `retry_config` | object | no |  |
| `timeout_seconds` | integer | no | min 1, max 60 |
| `raw_payload` | boolean | no |  |

### `zaphook_update_destination`

Updates a destination; send only what changes (`is_active` pauses or resumes it).

*Behavior: write, idempotent.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `dest_id` | UUID | yes |  |
| `name` | string | no | min length 1, max length 255 |
| `endpoint_url` | string | no |  |
| `http_method` | string | no |  |
| `description` | string | no |  |
| `headers` | object | no |  |
| `field_mapping` | object | no |  |
| `auth_config` | object | no |  |
| `retry_config` | object | no |  |
| `timeout_seconds` | integer | no | min 1, max 60 |
| `raw_payload` | boolean | no |  |
| `is_active` | boolean | no |  |

### `zaphook_delete_destination`

🗑️ Removes a destination; events stop being delivered to it.

*Behavior: destructive.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `channel_id` | UUID | yes |  |
| `dest_id` | UUID | yes |  |

## Events (delivery debugging)

### `zaphook_list_events`

Lists received and delivered events for standard and coexistence channels. Each item has `channel_type`. To find failures, use `has_errors=true` or `delivery_status=failed`. Paginate with `offset` in multiples of `limit`; the result includes `total`.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `limit` | integer | no | min 1, max 200 |
| `offset` | integer | no | min 0 |
| `channel_id` | UUID | no |  |
| `event_type` | string | no |  |
| `delivery_status` | string | no |  |
| `start_date` | ISO 8601 date-time | no |  |
| `end_date` | ISO 8601 date-time | no |  |
| `has_errors` | boolean | no |  |
| `channel_type` | `all` \| `standard` \| `coexistence` | no |  |

### `zaphook_get_event`

Event detail: original and processed payload, destination response, attempts and errors. Large responses may be truncated.

*Behavior: read-only.*

| Parameter | Type | Required | Limits |
|---|---|---|---|
| `event_id` | UUID | yes |  |
