# Team mode inferred from presence of `teams` field, not an explicit mode flag

The leaderboard enters team mode when the Contest State contains a `teams` array. No separate `mode: "solo" | "team"` flag exists.

An explicit flag would require keeping it in sync with the `teams` array — a `mode: "team"` with no `teams` field, or vice versa, would be a contradictory state. Using the data itself as the signal eliminates that class of inconsistency entirely. It also means all existing contest URLs continue to work without migration: the absence of `teams` is unambiguously solo mode.
