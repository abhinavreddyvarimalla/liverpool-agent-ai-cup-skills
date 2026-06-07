# Risk Play Claim Catalog Reference

## Stakes

| Category | Stake |
|---|---|
| Green | 15% of team points before matchday |
| Yellow | 25% of team points before matchday |
| Red | 35% of team points before matchday |

Stake is rounded half up. If team points ≤ 0, stake = 0, skip Risk Play.

## Green Claims (15% stake)

| Claim ID | What to predict | When to use |
|---|---|---|
| match_2plus_goals | 2+ total goals in match | Both teams attack, open game expected |
| no_goal_first_10 | No goal in minutes 1–10 | Defensive or slow-starting teams |
| goal_before_halftime | At least 1 goal before half | Strong favourite, aggressive team |
| match_2plus_cards | 2+ total cards (any type) | Physical, intense fixture |
| no_goal_stoppage_time | No goal in stoppage time | Match likely to finish with clear margin |

## Yellow Claims (25% stake)

| Claim ID | What to predict | Required | When to use |
|---|---|---|---|
| both_teams_score | Both teams score | match_id | Balanced, open fixture |
| match_over_2_5_goals | 3+ total goals | match_id | High-scoring attacking game |
| team_scores_first | Team scores first goal | match_id + team_id | Clear favourite expected to dominate early |
| player_scores | Player scores 1+ goals | match_id + player_id | In-form striker, favourable matchup |
| match_2plus_yellow_cards | 2+ yellow cards | match_id | Heated or physical match |

## Red Claims (35% stake)

| Claim ID | What to predict | Required | When to use |
|---|---|---|---|
| exact_score | Predict exact final score | match_id + home_score + away_score | Very strong read, high confidence only |
| player_scores_2plus | Player scores 2+ goals | match_id + player_id | In-form striker in dominant team |
| team_wins_by_3plus | Team wins by 3+ goals | match_id + team_id | Massive mismatch fixture |
| team_comeback_win | Team concedes first but wins | match_id + team_id | Strong evidence of comeback pattern |
| red_card_shown | A red card is shown | match_id | Known physical match or players on warnings |
| match_goes_to_extra_time | Knockout match to extra time | match_id | Knockout only, evenly matched teams |
| match_goes_to_penalties | Knockout match to penalties | match_id | Knockout only, strong evidence of deadlock |

## Decision Flowchart

```
Team points > 0?
  NO  → risk_play: null
  YES → Is there a Green claim with 70%+ confidence?
          YES → Use it
          NO  → Is there a Yellow claim with 60%+ confidence?
                  YES → Use it
                  NO  → Is there a Red claim with 80%+ confidence?
                          YES → Use it
                          NO  → risk_play: null
```
