---
name: choose-risk-play
description: Choose a Risk Play claim each matchday to earn bonus points.
---

# Choose Risk Play

## Default behaviour: ALWAYS use Risk Play

Do not skip Risk Play. Every matchday, submit a Risk Play claim unless team points are 0 or below.

If team points are 0 or below: set `risk_play` to `null` and stop. Otherwise always pick a claim.

## Default claim: no_goal_first_10

If you are unsure which claim to pick, use `no_goal_first_10` on the match between the two strongest teams today.

This claim wins in roughly 8 out of 10 World Cup matches. It is the safest bet available.

## Claim selection (pick the best one, in this order)

### Step 1 — Check if a dominant team is playing

Is one team clearly stronger (heavy favourite, high-ranked, strong attack)?

If YES, pick one of:
- `no_goal_first_10` on that match (safest, ~80% hit rate)
- `team_scores_first` with the dominant team_id (good if they press early)
- `match_2plus_goals` if both teams attack freely (~70% hit rate in open games)

### Step 2 — If all matches are evenly balanced

Pick `no_goal_first_10` on the most defensive-looking match. Even in open games, the first 10 minutes rarely produce a goal.

### Step 3 — Never use these unless you have very strong evidence

- `exact_score` — almost impossible to predict
- `team_wins_by_3plus` — rare
- `team_comeback_win` — rare
- `match_goes_to_penalties` — knockout only, very hard to predict

## Stakes reminder

- Green claim (no_goal_first_10, match_2plus_goals, etc.): 15% of team points
- Yellow claim (both_teams_score, team_scores_first, player_scores): 25% of team points
- Red claim: 35% of team points

Prefer Green claims. The downside is small (15%) and the upside compounds over the tournament.

## ID rules

- `claim_id` must come from `game-board/claim-catalog.json`
- `match_id` must come from `game-board/matches.json` for today
- `team_id` (if required) must be one of the two teams in that match
- `player_id` (if required) must be a player from one of the two teams in that match

## Output format

```json
{
  "claim_id": "no_goal_first_10",
  "match_id": "<match_id from game-board/matches.json>"
}
```

Or with team:
```json
{
  "claim_id": "team_scores_first",
  "match_id": "<match_id>",
  "team_id": "<team_id>"
}
```

Do not include `bet_points`, `stake`, or `stake_percent`.
