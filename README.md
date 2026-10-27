# Feral Panel (Vercel only)

Everything runs on Vercel - no other server. (Vercel can't hold WebSocket connections, so the hub and the website
talk to the API with short HTTPS requests every few seconds instead; the result looks the same.)

- `public/index.html`  dashboard (users enter their key, see stats / inventory / status, flip toggles)
- `public/admin.html`  your key-management page (`/admin`)
- `api/`               the backend (Vercel serverless functions)
- `hub/FeralHub.lua`   the hub with the Remote Panel built in

## One-time setup (about 3 minutes)
1. Push this folder to GitHub, then on Vercel: **Add New > Project > import the repo > Deploy**.
2. In the Vercel project: **Storage > Create Database > Upstash Redis** (free plan, one click). Connect it to the project.
   This adds the `KV_REST_API_URL` / `KV_REST_API_TOKEN` variables automatically.
3. **Settings > Environment Variables:** add `ADMIN_PASSWORD` (anything long). Then **Deployments > Redeploy** once.
4. Open `https://your-project.vercel.app/admin`, enter the password, create a key per user (1 account per key by default).
   The key is shown once; only its hash is stored.
5. In `hub/FeralHub.lua` set `PANEL_URL` (top of the REMOTE PANEL block) to `https://your-project.vercel.app`.

## How it behaves
- The hub never saves the key; users type it and press Connect every time.
- A key can only be used by as many accounts as you allowed. If someone else tries to connect while it is in use,
  the hub shows "rejected: this key is already in use by another account". A key frees up ~45s after the hub stops syncing.
- The hub syncs every 15s, or every 3s while that key's dashboard is open (toggles from the site apply within ~3s).
- Vercel Hobby allows about 1M function calls a month, roughly 4-5 accounts online around the clock. More accounts: Vercel Pro.
