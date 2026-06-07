---
name: choose-risk-play
description: Decide whether to use a valid Risk Play from the current claim catalog.
---

Use this skill when deciding whether the daily answer should include a Risk Play.

Rules:
1. Risk Play is optional.
2. If there is any uncertainty, return risk_play as null.
3. If team points before the matchday are 0 or lower, return risk_play as null.
4. Use only claim IDs from game-board/claim-catalog.json.
5. Use only match IDs from game-board/matches.json.
6. For team claims, the team_id must be home or away in the selected match.
7. For player claims, the player_id must belong to one of the teams in the selected match.
8. Do not include bet_points, stake, or stake_percent.
9. Never invent claims, matches, teams, or players.

Preference order:
1. Prefer Green claims.
2. Use Yellow claims only when evidence is strong.
3. Avoid Red claims unless evidence is overwhelming.
4. Skip Risk Play instead of forcing a weak prediction.

Good Green choices:
1. Use match_2plus_goals when both teams have attacking indicators.
2. Use goal_before_halftime when at least one team has strong attacking indicators.
3. Use no_goal_first_10 when the match looks cautious or defensive.
4. Use match_2plus_cards when the matchup looks physical.

Final check:
1. risk_play must be null or a valid claim object.
2. All required fields for the selected claim must be present.
3. No extra stake fields should be included.
4. The strategy text must correctly describe the selected claim category.
