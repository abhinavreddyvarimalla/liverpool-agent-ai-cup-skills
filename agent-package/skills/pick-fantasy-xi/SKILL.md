---
name: pick-fantasy-xi
description: Choose a valid Fantasy XI and optional Risk Play from the current tournament game board.
---

Use this skill when answering the daily AI Agent Fantasy World Cup prompt.

Core rules:
1. Read the official files in the workspace:
   - prompt.md
   - game-board/matchday.json
   - game-board/matches.json
   - game-board/players.json
   - game-board/teams.json
   - game-board/claim-catalog.json
   - output-format/
2. Return exactly one plain JSON object.
3. Do not return Markdown, code fences, comments, or extra text.
4. Use the team_id and matchday_id shown in the prompt or game board.
5. Pick exactly 11 player IDs from the eligible player list.
6. Use exactly:
   - 1 goalkeeper
   - 3 to 5 defenders
   - 3 to 5 midfielders
   - 1 to 3 forwards
7. Use only valid player IDs from the current game board.
8. Do not invent players, teams, matches, IDs, or scoring rules.

Fantasy XI strategy:
1. Prefer players marked eligible.
2. Prefer players likely to start.
3. Prefer players with strong current or recent World Cup metrics from the game board.
4. Prefer attacking midfielders and forwards from stronger teams.
5. Prefer defenders and goalkeepers from teams with reasonable clean-sheet chances.
6. Avoid players with weak availability or low expected minutes when the board gives that context.
7. If two players look similar, prefer the player from the stronger team.
8. Check the final lineup satisfies all position rules before answering.

Risk Play strategy:
1. Risk Play is optional.
2. Skip Risk Play by returning null when no claim looks clearly favorable.
3. Prefer Green claims over Yellow or Red claims unless the evidence is strong.
4. Prefer match_2plus_goals when both teams look attacking or the match has good scoring indicators.
5. Prefer goal_before_halftime when at least one team has strong attacking indicators.
6. Prefer no_goal_first_10 when the match looks defensive or cagey.
7. Prefer match_2plus_cards when the matchup looks physical.
8. Avoid Red claims unless the evidence is extremely strong.
9. Never combine a match_id with a team_id or player_id from a different match.
10. For team_id claims, the team must be home or away in that match.
11. For player_id claims, the player must belong to one of the teams in that match.
12. If team points before the matchday are 0 or lower, skip Risk Play.

Before final answer, validate:
1. There are exactly 5 top-level keys:
   - team_id
   - matchday_id
   - fantasy_xi
   - risk_play
   - strategy
2. fantasy_xi has exactly 11 player IDs.
3. The formation is valid.
4. Every selected player ID exists in game-board/players.json.
5. risk_play is either null or a valid claim object from the claim catalog.
6. The answer is plain JSON only.

Example answer shape:

{
  "team_id": "use-team-id-from-prompt",
  "matchday_id": "use-matchday-id-from-game-board",
  "fantasy_xi": [
    "player_id_1",
    "player_id_2",
    "player_id_3",
    "player_id_4",
    "player_id_5",
    "player_id_6",
    "player_id_7",
    "player_id_8",
    "player_id_9",
    "player_id_10",
    "player_id_11"
  ],
  "risk_play": null,
  "strategy": "Chose a valid balanced lineup using likely starters and skipped Risk Play because no claim was clearly favorable."
}
