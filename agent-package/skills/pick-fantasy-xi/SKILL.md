---
name: pick-fantasy-xi
description: Pick a valid Fantasy XI using a strict 1-4-4-2 formation.
---

# Pick Fantasy XI

Use this skill to answer the daily AI Agent Fantasy World Cup prompt.

Return one plain JSON object only.

## Non-negotiable lineup

Pick exactly:

- 1 GK
- 4 DEF
- 4 MID
- 2 FWD

Total: exactly 11 players.

Do not use any other formation.

## Critical rules

1. Select exactly one player where `position = GK`.
2. After selecting one GK, do not select any other GK.
3. A goalkeeper can only fill the GK slot.
4. Do not use a GK as a defender, midfielder, forward, backup, bench, or utility player.
5. There is no bench.
6. There are no utility slots.
7. If `fantasy_xi` contains 2 or more GK players, the answer is invalid. Remove every extra GK before answering.
8. Replace extra GK players with MID players first until there are 4 MID.
9. Then replace with DEF players until there are 4 DEF.
10. Then replace with FWD players until there are 2 FWD.

## Selection order

Follow this order exactly:

1. Pick the best eligible GK.
2. Pick the best 4 eligible DEF players.
3. Pick the best 4 eligible MID players.
4. Pick the best 2 eligible FWD players.
5. Stop.

Do not keep selecting players after the slots are full.

## Player rules

Use only eligible players from `game-board/players.json`.

Use each player's position exactly:

- `GK` = goalkeeper only
- `DEF` = defender only
- `MID` = midfielder only
- `FWD` = forward only

Do not invent player IDs.

Do not duplicate player IDs.

## Ranking strategy

Within each position group, prefer:

1. Likely starters
2. Players likely to play 60+ minutes
3. Strong team context
4. Good recent metrics from the game board
5. Clean-sheet potential for GK and DEF
6. Goal and assist potential for MID and FWD

Validity is more important than upside.

## Risk Play

Risk Play is optional.

Use `risk_play: null` unless a Green claim is clearly favorable.

If choosing Risk Play:

1. Use only claim IDs from `game-board/claim-catalog.json`.
2. Use only match IDs from `game-board/matches.json`.
3. Do not include `bet_points`, `stake`, or `stake_percent`.
4. Prefer Green claims.
5. If unsure, use `null`.

## Final check

Before returning JSON, count the selected player positions.

The answer is valid only if:

- GK = 1
- DEF = 4
- MID = 4
- FWD = 2
- Total = 11

If GK is 2 or more, remove extra GK immediately.

If the counts are not exactly correct, rebuild the lineup from scratch.

## Output format

Return exactly these top-level keys:

- `team_id`
- `matchday_id`
- `fantasy_xi`
- `risk_play`
- `strategy`

Example shape only:

{
  "team_id": "use-team-id-from-prompt",
  "matchday_id": "use-matchday-id-from-game-board",
  "fantasy_xi": [
    "one_goalkeeper_id",
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
  "strategy": "Used a strict valid 1-4-4-2 lineup with exactly one goalkeeper, four defenders, four midfielders, and two forwards."
}
