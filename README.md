# LC Contest Tracker

Live leaderboard for internal LeetCode contests. No backend, no database. The entire contest config is encoded in the URL hash.

## Usage

1. Open `index.html` → fill in name, start time, duration, participants, problems
2. Optionally set **Team Size** to group participants into teams (see [Team mode](#team-mode))
3. Click **Open Contest →** or copy the link to share
4. Anyone with the link sees live standings — no login, no setup

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

## Participants format

One participant per line. Use `username` for LeetCode-username-only, or `username|Display Name` to show a custom name on the leaderboard while the link still points to the real profile.

```
alice
bob|Bob Smith
charlie|Smith, Charlie
```

Display names can contain anything — commas, colons, parentheses, spaces. The only character that cannot appear in a display name is `|`, which is also never valid in a LeetCode username.

## Team mode

Set **Team Size** to 2 or more in the builder to enable team mode. Participants are randomly assigned to teams of that size; if the count doesn't divide evenly, the leftover participant joins a random existing team.

Each team gets a randomly assigned space-themed name (Aurora, Nebula, Quasar, …).

**Scoring in team mode:**

- The leaderboard shows one row per team, ranked by problems solved then total time.
- A problem is solved for the team the moment any member gets an accepted submission. The earliest acceptance time is used for scoring.
- Wrong attempts from all team members are summed, but only those submitted before the team's first acceptance count.
- If more than one member solves the same problem, the cell shows `×N` to indicate how many members solved it. The best (earliest) time is always used for ranking.

Team assignments are generated once in the builder and stored in the URL — every viewer sees the same teams. To reshuffle, edit the Team Size field or the participant list; to assign teams manually, edit the `teams` array in the Config JSON textarea.

## Config JSON

The JSON textarea in the builder accepts and exports the following fields:

**Solo mode:**

```json
{
  "title": "Q3 2025 Internal Contest",
  "startLocal": "2025-09-01T10:00",
  "duration": 90,
  "users": ["o_xot_nik", "kjsdf983"],
  "displayNames": {
    "o_xot_nik": "Andrii",
    "kjsdf983": "Anton"
  },
  "problems": ["two-sum", "add-two-numbers"]
}
```

**Team mode** (adds `teamSize` and `teams`):**

```json
{
  "title": "Q3 2025 Internal Contest",
  "startLocal": "2025-09-01T10:00",
  "duration": 90,
  "users": ["o_xot_nik", "kjsdf983", "user7820aV", "vanyadndz"],
  "displayNames": {
    "o_xot_nik": "Andrii",
    "kjsdf983": "Anton"
  },
  "problems": ["two-sum", "add-two-numbers"],
  "teamSize": 2,
  "teams": [
    { "name": "Nebula", "members": ["o_xot_nik", "vanyadndz"] },
    { "name": "Quasar", "members": ["kjsdf983", "user7820aV"] }
  ]
}
```

`displayNames` is optional — omit it or leave it empty to show raw LeetCode usernames. Any user not present in `displayNames` falls back to their username on the leaderboard.

`teams` is optional — omit it (or set `teamSize` to 1) for solo mode. When present, the leaderboard scores teams instead of individuals.

## Requirements

- Participants must have **public** LeetCode profiles
- Problems must be live on LeetCode and submitted there during the contest window
- The Cloudflare Worker must be deployed and its URL set as `PROXY` in `index.html`

## Files

| File | Purpose |
|------|---------|
| `index.html` | Entire app — builder form, leaderboard, styles, all logic |
| `worker.js` | Cloudflare Worker — CORS proxy for LeetCode GraphQL |
