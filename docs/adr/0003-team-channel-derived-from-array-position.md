# Team communication channel derived from array position, not stored

The leaderboard shows "Channel N" under each team's member list, in team mode only, to indicate which communication channel that team should use. `N` is the team's 1-based position in the Contest State's `teams` array — it is not a field stored on the team object.

`teams` array order is already frozen into the Contest State at generation time and never reordered by the leaderboard (see `0001-team-assignments-stored-in-url-state.md`). Deriving the channel from array position gets the same stability guarantee — identical across reloads and for the whole contest — without adding a new field, bumping the state version, or requiring migration for contest links already shared. Solo mode (no `teams` array) never shows a channel, consistent with team mode being inferred from `teams`' presence (see `0002-team-mode-inferred-from-teams-field.md`).

## Consequences

Manually reordering the `teams` array in the builder's JSON textarea shifts channel numbers along with it, since a team's channel has no identity independent of its array position. This mirrors existing behavior, where `teams` array order has no other visible meaning today.
