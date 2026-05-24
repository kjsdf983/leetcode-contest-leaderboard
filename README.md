# LC Contest Tracker

Live leaderboard for internal LeetCode contests. No backend, no database. The entire contest config is encoded in the URL hash.

## Usage

1. Open `index.html` → fill in name, start time, duration, participants, problems
2. Click **Open Contest →** or copy the link to share
3. Anyone with the link sees live standings — no login, no setup

The link is fully self-contained. Sharing it is all that's needed.

## How it works

**URL state** — config is JSON-serialized, LZString-compressed, and stored in the URL hash (`#...`). A typical config (15 users, 4 problems) fits in ~350 characters.

**Polling** — the viewer fetches the last 20 submissions for all participants in a single batched GraphQL request every 30 seconds. Submissions are deduplicated across poll cycles so no data is lost.

**Scoring** — ranked by problems solved (desc), then total solve time (asc). Wrong attempts before the first acceptance are shown per problem. Submissions after acceptance are ignored.

**Proxy** — LeetCode's GraphQL API requires a `Referer` header that browsers cannot send cross-origin. Requests go through a Cloudflare Worker (`worker.js`) that adds the header and relays the response.

## Running

```bash
# Local — just open the file
open index.html

# Deploy to GitHub Pages
git init && git add . && git commit -m "init"
gh repo create my-lc-tracker --public --push --source=.
# Enable Pages: Settings → Pages → Branch: main → / (root)
```

## Requirements

- Participants must have **public** LeetCode profiles
- Problems must be live on LeetCode and submitted there during the contest window
- The Cloudflare Worker must be deployed and its URL set as `PROXY` in `index.html`

## Files

| File | Purpose |
|------|---------|
| `index.html` | Entire app — builder form, viewer, styles, all logic |
| `worker.js` | Cloudflare Worker — CORS proxy for LeetCode GraphQL |
