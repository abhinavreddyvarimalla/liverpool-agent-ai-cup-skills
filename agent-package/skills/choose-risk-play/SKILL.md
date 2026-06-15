---
name: choose-risk-play
description: Choose a Risk Play claim each matchday to earn bonus points while avoiding obviously bad bets.
---

# Choose Risk Play

## Track record so far

- Jun 12: `no_goal_first_10` → CORRECT (+13)
- Jun 13: `no_goal_first_10` → CORRECT (+18)
- Jun 14: `no_goal_first_10` → WRONG (-20), match ended 7-1 with a goal in minute 6

`no_goal_first_10` is a reasonable default (won 2 of 3 so far) but fails badly when one team is a massive favourite likely to attack from the first whistle.

## Step 0 — Check team points

Read `game-board/standings-before.json`.
- If team points are 0 or below: `risk_play: null`, stop.
- Otherwise continue. Do not skip by default — Risk Play has been net positive (+13, +18, -20 = +11 over 3 days).

## Step 1 — Identify the biggest mismatch match today

Look at `game-board/matches.json` and `game-board/teams.json`. Find the match with the largest gap in team strength (rankings, recent goal difference, attack/defence metrics).

## Step 2 — Pick the claim based on match type

### If there IS a clear heavy-favourite mismatch:
- Use `match_2plus_goals` (Green) on that match — near-certain when one side dominates
- Or `team_scores_first` (Yellow) with the favourite's team_id — favourites usually score first
- Avoid `no_goal_first_10` on this match specifically

### If today's matches are all close/balanced (no big mismatch):
- Use `no_goal_first_10` (Green) on the most cautious-looking fixture — this is your proven 2/3 default
- Or `both_teams_score` (Yellow) if both teams in a balanced match have decent attack

### If genuinely unsure:
- Default to `no_goal_first_10` on the match between the two most evenly-matched/defensive teams (not the biggest mismatch match)

## Step 3 — Red claims

Only with very strong evidence:
- `team_wins_by_3plus` if a team has a track record of big wins against similar opponents
- `player_scores_2plus` for a confirmed-starting in-form striker in a favourable matchup

## ID validation

- `claim_id` from `game-board/claim-catalog.json`
- `match_id` from `game-board/matches.json` for today
- `team_id`/`player_id` (if required) must belong to that match

## Output format

```json
{"claim_id": "no_goal_first_10", "match_id": "<match_id>"}
```
or
```json
{"claim_id": "match_2plus_goals", "match_id": "<match_id>"}
```
or
```json
{"claim_id": "team_scores_first", "match_id": "<match_id>", "team_id": "<team_id>"}
```
or
```json
"risk_play": null
```

## Stakes

- Green: 15% of team points
- Yellow: 25% of team points
- Red: 35% of team points

## AGGRESSIVE MODE — consider Yellow/Red for extreme mismatches

For teams trying to close a points gap, Green claims (15% stake) cap upside. When a match shows a genuinely extreme mismatch (e.g. a top-ranked team vs a team with a poor goal difference and weak recent form), consider:

- `team_scores_first` (Yellow, 25%) with the dominant team's `team_id` — favourites in extreme mismatches almost always score first
- `player_scores` (Yellow, 25%) for the dominant team's confirmed-starting top striker with a recent goal record
- `player_scores_2plus` (Red, 35%) ONLY if that striker has scored 2+ in a recent match against similar opposition

Use Yellow/Red selectively — not every day, only when the mismatch is clear and the player/team picked is a confirmed starter with strong recent form. On balanced or unclear matchdays, stick to the Green default (`no_goal_first_10` or `match_2plus_goals` per the match-type logic above).
