# Team assignments stored in Contest State, not computed at view time

The builder randomly assigns participants to teams and writes the result directly into the Contest State (URL hash). The leaderboard reads the assignment as-is â it never re-derives teams from the participant list or team size.

The alternative was computing teams at view time from a seeded RNG using the participant list as input. We rejected this because any edit to the participant list â even reordering â would silently reshuffle teams, and different viewers could observe different assignments if their client clocks or RNG implementations differed. Storing the assignment in the URL makes it immutable and identical for every viewer.

## Consequences

`teamSize` is a builder-only control; it does not appear in the URL state. Only the resolved `teams` array (with names and member lists) is persisted.
