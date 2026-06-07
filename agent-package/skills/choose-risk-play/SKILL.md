---
name: choose-risk-play
description: Decide whether to use Risk Play each matchday, which claim to pick, and which match to attach it to, based on current team points, available claims, and match context.
---

# Choose Risk Play

Use this skill when deciding whether and how to submit a Risk Play claim each matchday.

## Critical Rule: Never Force a Weak Prediction

Risk Play is optional. Skipping Risk Play is always better than making a bad prediction that loses points. Only place a Risk Play when you have genuine confidence in the claim.

## Step 0 — Check team points

Read `game-board/standings-before.json` for your team's current point total.

- If team points are **0 or below**: set `risk_play` to `null` and stop. Stake is 0 so there is nothing to gain.
- If team points are **above 0**: continue to Step 1.

## Step 1 — Read available claims

Read `game-board/claim-catalog.json` to see which claims are available today. Only pick a claim that appears in this file.

## Step 2 — Evaluate each match from `game-board/matches.json`

For each match, assess:
- Which team is the favourite (use `teams.json` strength, standings, or recent form)?
- Is this a balanced fixture (both teams strong) or one-sided?
- Is this a Group Stage or Knockout match?

Use the match context to decide which claim types apply.

## Step 3 — Select the best claim using this hierarchy

Work through this list top to bottom. Pick the **first claim you are confident in**. If you reach the end without confidence, skip Risk Play (return `null`).

### Green Claims (15% stake — lower risk, lower reward)

**match_2plus_goals** — `match_id` only
- Use when the match features two attacking teams and a low chance of a 0-0 or 1-0 scoreline.
- World Cup group stage games between competitive teams regularly hit 2+ goals.
- Confidence threshold: pick this if you believe there is a 70%+ chance of 2+ goals.

**goal_before_halftime** — `match_id` only
- Use when one team is a heavy favourite and tends to score early.
- Avoid for defensive, cautious teams who tend to score late.

**match_2plus_cards** — `match_id` only
- Use for matches between intense rivals or teams known for physical play.
- Avoid for clean technical matches.

**no_goal_first_10** — `match_id` only
- Use when both teams are defensively organised and unlikely to open quickly.
- High base rate: most matches do not have a goal in the first 10 minutes.

**no_goal_stoppage_time** — `match_id` only
- Use when the match is likely to end with a comfortable winning margin, reducing desperation play.
- High base rate but risky in close matches.

### Yellow Claims (25% stake — medium risk)

**both_teams_score** — `match_id` only
- Use for balanced fixtures where both teams have attacking quality.
- Avoid one-sided matches where the weaker team may be shut out.

**match_over_2_5_goals** — `match_id` only (3+ total goals)
- Use only for genuinely open, attacking fixtures. Higher bar than 2+ goals — be more selective.

**team_scores_first** — `match_id` + `team_id`
- Use when one team is a clear favourite and tends to dominate early.
- Only submit a valid `team_id` that belongs to the selected `match_id`.

**player_scores** — `match_id` + `player_id`
- Use for a striker in exceptional form who is the team's primary goal threat.
- The player must appear in `game-board/players.json` and belong to one of the teams in the match.
- Do not pick a player who has injury doubt.

**match_2plus_yellow_cards** — `match_id` only
- Use for intense, physical, or high-pressure matches.

### Red Claims (35% stake — high risk, high reward)

Only use Red claims if you have a very strong signal. Getting one wrong loses 35% of your score.

**exact_score** — `match_id` + `home_score` + `away_score`
- Only use if you have a very strong read on a likely scoreline, such as a heavy favourite in a must-win scenario with a predictable margin.
- Avoid this unless you have clear evidence.

**player_scores_2plus** — `match_id` + `player_id`
- Only use for an in-form striker with multiple goals this tournament, in a favourable fixture.

**team_wins_by_3plus** — `match_id` + `team_id`
- Only use when there is a massive mismatch. Rare — most World Cup games are competitive.

**team_comeback_win** — `match_id` + `team_id`
- Very hard to predict. Only use if strong form data suggests a team frequently concedes first but wins.

**red_card_shown** — `match_id` only
- Only use for matches known for physical intensity or with players on disciplinary warnings.

**match_goes_to_extra_time** / **match_goes_to_penalties** — `match_id` only
- Only valid in Knockout matches. Never use in Group Stage (draws end at 90 min).
- Use for evenly matched knockout ties with historical records of close finishes.

## Step 4 — Confirm IDs are valid

Before submitting:
- `match_id` must come from `game-board/matches.json` for today.
- If the claim requires `team_id`: the team must be home or away in that match, from `game-board/teams.json`.
- If the claim requires `player_id`: the player must be in `game-board/players.json` and play for one of the teams in that match.
- Never combine a match from one row with a team or player from a different row.

## Step 5 — Output format

Return one of:
```
"risk_play": {
  "claim_id": "<claim_id from catalog>",
  "match_id": "<valid match_id>"
}
```
Or with optional extra fields if required by the claim:
```
"risk_play": {
  "claim_id": "player_scores",
  "match_id": "match_003",
  "player_id": "player_042"
}
```
Or skip entirely:
```
"risk_play": null
```

## References

See `references/risk-play-catalog.md` for a quick summary of all claims and their confidence requirements.
