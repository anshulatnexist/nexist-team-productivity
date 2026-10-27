# Nexist Team Productivity dashboard

A single static page (`public/index.html`) showing each Nexist employee's WorkTime
activity, individually and by team, with employee filter toggles.

## How it stays current

The page has no backend. On every page load it fetches fresh rows directly from a
Google Sheet (via the public CSV export endpoint) and re-renders everything from
those rows. To publish new numbers: open the Sheet, paste in the next WorkTime
export, done — no redeploy needed.

- Google Sheet: https://docs.google.com/spreadsheets/d/10c89SwrE9mZb59TJ9N5TW5DbcqOarWKLPHy9myuFOVU/edit
- Sheet must stay shared as "Anyone with the link — Viewer" (Share → General access) or the live fetch fails and the page falls back to the snapshot baked in at build time.
- WorkTime (cloud.worktime.com) has no API on Nexist's current Basic plan, so the Sheet is filled by hand from exported reports until that changes.

## Deploying (Cloudflare Pages, Git integration)

1. Cloudflare dashboard → Workers & Pages → Create → Pages → **Connect to Git**.
2. Pick this repo (`nexist-team-productivity`), branch `main`.
3. Build settings: framework preset **None**, build command **(leave blank)**,
   build output directory **`public`** (matches `wrangler.toml`).
4. Deploy. Cloudflare gives you a `*.pages.dev` URL.
5. In that Pages project → Custom domains → add `nexist.cloud` (or a subdomain of it),
   and point its DNS at Cloudflare per the prompt (automatic if the zone is already
   on this Cloudflare account).

Pushing a new commit to `main` (a code/design change to the page) triggers an
automatic redeploy. A plain data refresh (new WorkTime numbers) does **not**
need a push — just update the Google Sheet.
