# Vercel deployment

This project is configured for Vercel static hosting plus Node.js Functions. Browser requests now use `/api/<function>`; the Vercel rewrite also preserves the older `/.netlify/functions/<function>` URLs.

## Project settings

- Import this repository into Vercel and set the project root to `chat-app` (the repository root containing `vercel.json`).
- `vercel.json` serves static files from `public/`, exposes the API adapter under `/api/*`, maps `/Admin` to the admin page, and keeps the previous Netlify function paths working.
- Use the regular Vercel Node.js runtime; do not select Edge Runtime because the handlers use Node crypto and the Supabase SDK's Node transport.
- Configure the following environment variables for Preview and Production deployments:
  - `SUPABASE_URL`
  - `SUPABASE_SERVICE_ROLE_KEY` — secret; server-side only
  - `ADMIN_PASSWORD`
- The Supabase publishable key in the browser is intentionally public. Never add the service-role key to frontend code.

The API adapter at `api/[...path].js` dispatches to the existing handler modules in `netlify/functions/`, adapting Vercel requests to their existing Netlify-style event shape. Keeping those modules lets Netlify remain usable if desired, while the browser is now pointed at Vercel's `/api` routes.

## Local preview

Install dependencies and run `npx vercel dev` from the project root. Vercel CLI will prompt for project linking if needed. Set the environment variables using `vercel env add` or a local `.env.local` file (never commit it).

## Before production

Apply the required Supabase migrations in their documented order, including `20261126_chat_api_keys.sql` for API key issuance. Confirm Supabase Auth's Site URL and redirect allowlist include the Vercel production and preview URLs you intend to use. Then test login, messages, server operations, DMs, API key create/revoke, and API message delivery on a Vercel Preview deployment before promoting it to Production.
