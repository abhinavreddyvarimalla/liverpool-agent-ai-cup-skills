---
name: pick-fantasy-xi
description: Choose a valid Fantasy XI and optional Risk Play from the current tournament game board.
---

Use this skill when answering the daily AI Agent Fantasy World Cup prompt.

The most important goal is to return a valid answer. A valid answer is better than a clever but invalid answer.

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
3. Do not return Markdown, code fences, comments, explanations outside JSON, or extra text.
4. Use the team_id shown in the prompt.
5. Use the matchday_id shown in game-board/matchday.json or the prompt.
6. Use only official IDs from the current game board.
7. Do not invent players, teams, matches, IDs, or scoring rules.
8. Finish quickly. Do not perform long research or network-dependent work.

Required output:
Return exactly these 5 top-level keys:
1. team_id
2. matchday_id
3. fantasy_xi
4. risk_play
5. strategy

Formation lock:
1. Use this default formation every time:
   - Exactly 1 goalkeeper
   - Exactly 4 defenders
   - Exactly 4 midfielders
   - Exactly 2 forwards
2. This means the lineup must have exactly 11 players total.
3. Do not choose more than 2 forwards.
4. Do not choose fewer than 2 forwards unless the eligible player pool makes 2 forwards impossible.
5. If the best available players include many forwards, still choose only 2 forwards and fill the rest with midfielders or defenders.
6. Before returning JSON, count positions again.
7. If forwards are more than 2, remove forwards until there are exactly 2 forwards.
8. If defenders are not exactly 4, adjust the lineup until there are exactly 4 defenders.
9. If midfielders are not exactly 4, adjust the lineup until there are exactly 4 midfielders.
10. If goalkeeper count is not exactly 1, adjust the lineup until there is exactly 1 goalkeeper.

Player selection rules:
1. Pick exactly 11 player IDs from game-board/players.json.
2. Every selected player must be eligible for the current matchday.
3. Every selected player ID must exist in the official player list.
4. Do not select duplicate player IDs.
5. Do not select a player who is not in the official eligible player pool.
6. If selected player data has position labels, obey those labels exactly.
7. Treat GK as goalkeeper.
8. Treat DEF as defender.
9. Treat MID as midfielder.
10. Treat FWD as forward.
11. Do not classify a forward as a midfielder just to fit the formation.
12. Do not classify a midfielder as a forward unless the board position says forward.

Fantasy XI strategy:
1. Prefer likely starters.
2. Prefer players with strong current or recent World Cup metrics from the game board.
3. Prefer attacking midfielders and forwards from stronger teams.
4. Prefer defenders and goalkeepers from teams with reasonable clean-sheet chances.
5. Prefer players from favored teams when the board indicates team strength.
6. Avoid players with weak availability, low expected minutes, or injury concerns when the board gives that context.
7. If two players look similar, prefer the player from the stronger team.
8. If choosing between an extra forward and a midfielder, choose the midfielder because the formation is locked to exactly 2 forwards.
9. Validity beats upside. Do not break the formation rules for a high-upside player.

Risk Play strategy:
1. Risk Play is optional.
2. Return risk_play as null when no claim looks clearly favorable.
3. If team points before the matchday are 0 or lower, return risk_play as null.
4. Prefer Green claims over Yellow or Red claims unless the evidence is strong.
5. Green claims are lower risk because they stake 15%.
6. Yellow claims stake 25%.
7. Red claims stake 35% and should usually be avoided.
8. Never create a Risk Play claim that is not in game-board/claim-catalog.json.
9. Never combine a match_id with a team_id or player_id from a different match.
10. For team_id claims, the team must be the home or away team for that match.
11. For player_id claims, the player must belong to one of the teams in that match.
12. Required fields must exactly match the selected claim type.
13. Do not include bet_points, stake, or stake_percent in the answer.

Preferred Risk Play choices:
1. Prefer match_2plus_goals when both teams look attacking or the match has good scoring indicators.
2. Prefer goal_before_halftime when at least one team has strong attacking indicators.
3. Prefer no_goal_first_10 when the match looks defensive, cautious, or cagey.
4. Prefer match_2plus_cards when the matchup looks physical.
5. Prefer team_scores_first only when one team is clearly stronger. This is a Yellow Risk Play.
6. Avoid exact_score unless the evidence is overwhelming.
7. Avoid player_scores_2plus unless a specific elite scorer has extremely strong evidence.
8. Avoid red_card_shown unless the matchup has strong card evidence.
9. If unsure, skip Risk Play by returning null.

Risk Play wording:
1. The strategy text must match the selected claim category.
2. If selecting team_scores_first, describe it as a Yellow Risk Play, not Green.
3. If selecting match_2plus_goals, no_goal_first_10, goal_before_halftime, match_2plus_cards, or no_goal_stoppage_time, describe it as a Green Risk Play.
4. If risk_play is null, say Risk Play was skipped because no claim was clearly favorable.

Final validation checklist:
Before returning the final JSON, verify all of these:
1. The answer is one JSON object only.
2. There are exactly 5 top-level keys:
   - team_id
   - matchday_id
   - fantasy_xi
   - risk_play
   - strategy
3. fantasy_xi contains exactly 11 unique player IDs.
4. The lineup has exactly 1 goalkeeper.
5. The lineup has exactly 4 defenders.
6. The lineup has exactly 4 midfielders.
7. The lineup has exactly 2 forwards.
8. No selected player ID is invented.
9. No selected player ID is duplicated.
10. risk_play is either null or a valid claim object from the claim catalog.
11. If risk_play uses match_id, that match_id exists in game-board/matches.json.
12. If risk_play uses team_id, that team belongs to the selected match.
13. If risk_play uses player_id, that player belongs to one of the teams in the selected match.
14. The strategy is brief and truthful.
15. The strategy does not claim a Yellow Risk Play is Green.
16. The output has no Markdown fences.

If validation fails internally:
1. Fix the Fantasy XI first.
2. If there is any uncertainty about Risk Play validity, set risk_play to null.
3. Return a conservative valid lineup instead of an aggressive invalid lineup.

Example answer shape only. Replace all IDs with real IDs from the current game board:

{
  "team_id": "use-team-id-from-prompt",
  "matchday_id": "use-matchday-id-from-game-board",
  "fantasy_xi": [
    "goalkeeper_id",
    "defender_id_1",
    "defender_id_2",
    "defender_id_3",
    "defender_id_4",
    "midfielder_id_1",
    "midfielder_id_2",
    "midfielder_id_3",
    "midfielder_id_4",
    "forward_id_1",
    "forward_id_2"
  ],
  "risk_play": null,
  "strategy": "Chose a valid 1-4-4-2 lineup using likely starters and skipped Risk Play because no claim was clearly favorable."
}
