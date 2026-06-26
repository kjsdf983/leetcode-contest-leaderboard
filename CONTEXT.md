# LC Contest Tracker

A no-backend, URL-driven live leaderboard for internal LeetCode contests. All contest state lives in the URL hash; no server or database is involved.

## Language

### Core Concepts

**Contest**: A timed event defined by a title, start time, duration, a list of participants, and a set of problems. Fully described by the Contest State.
_Avoid_: Competition, session, round

**Contest State**: The single JSON object, LZString-compressed into the URL hash, that fully defines a contest. The builder produces it; the leaderboard consumes it.
_Avoid_: Config, payload, URL params

**Builder**: The setup page where an organiser configures a contest and generates a shareable URL.
_Avoid_: Editor, form, setup page

**Leaderboard**: The live standings page. Driven entirely by the Contest State in the URL — no server calls except the LeetCode GraphQL proxy.
_Avoid_: Viewer, dashboard

### Participants & Teams

**Participant**: A LeetCode user registered in a contest. In solo mode, the scoring unit. In team mode, a member of a Team.
_Avoid_: User, player, competitor

**Display Name**: A human-readable alias for a LeetCode username, stored in `displayNames` and shown everywhere the username would otherwise appear.
_Avoid_: Alias, nickname, friendly name

**Team**: A named group of Participants that competes as a single scoring unit in team mode.
_Also acceptable_: Group

**Team Mode**: Active when the Contest State contains a `teams` array. The leaderboard scores Teams instead of individual Participants. Absent `teams` means solo mode — the current default.
_Avoid_: Group mode, multiplayer mode

**Scoring Unit**: The entity that occupies one row on the leaderboard — a Participant in solo mode, a Team in team mode.

### Scoring

**Solve Time**: Seconds elapsed from contest start to a Participant's first accepted submission for a given problem. The basis for all time-based ranking.
_Avoid_: Completion time, elapsed time

**First Team Acceptance**: The earliest Solve Time among all team members for a given problem. Marks that problem as solved for the Team and is the time used for team scoring.
_Avoid_: Team solve time, group acceptance

**Wrong Attempts**: The count of failed submissions before the first acceptance for a problem. In team mode, summed across all members before the First Team Acceptance.
_Avoid_: Failures, errors, penalty
