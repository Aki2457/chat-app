# Chat API — send messages

The Chat API accepts scoped personal API keys in the format `chat-api-v1[SECRET]`. Each key is bound to one server and posts as the username that owns the key. Keys can be created and revoked from **Quick Links → Chat API keys** in the chat UI.

## Create and store a key

1. Sign in to the chat app and open **Chat API keys**.
2. Give the key a descriptive name and select a server where your account is a member.
3. Copy the full key when it is shown. The secret is displayed only once; the app stores only its SHA-256 hash.
4. Revoke it in the same dialog if it is lost or no longer needed.

Keep API keys in a server-side secret manager or environment variable. Do not place them in public browser code, source repositories, URLs, or logs.

## Send a message

`POST https://YOUR_CHAT_HOST/api/chat-api`

Headers:

```http
Authorization: Bearer chat-api-v1[YOUR_SECRET]
Content-Type: application/json
```

JSON body:

```json
{
  "content": "Hello from my integration"
}
```

The key determines the destination server. `server_id` may optionally be provided, but it must match the server the key is scoped to. Messages are limited to 1,000 characters. Successful sends return HTTP 201 with the inserted message record. The endpoint currently supports sending only; it does not provide message-history reads or DM sending.

## Errors

- `400` — missing/empty content, too long, or the active-key limit (20) has been reached.
- `401` — missing, unknown, or revoked API key.
- `403` — key owner is banned, no longer belongs to its server, or the optional `server_id` does not match the key.

## Database setup

Apply the migrations in order: `20261124_multi_server_accounts.sql`, `20261125_profile_pics_dms.sql`, then `20261126_chat_api_keys.sql`. The key-management and message endpoints use the Vercel server-side `SUPABASE_URL` and `SUPABASE_SERVICE_ROLE_KEY` environment variables. Keep the service-role key server-side only.
