---
name: pick-fantasy-xi
description: Pick a valid Fantasy XI using a strict 1-4-4-2 formation, prioritizing players who will actually start and play.
---

# Pick Fantasy XI

Use this skill to answer the daily AI Agent Fantasy World Cup prompt.

Return one plain JSON object only.

## THE #1 RULE: DO NOT PICK PLAYERS WHO WON'T PLAY

Across three matchdays, the single biggest point loss was picking players who returned "Did Not Play Or Missing Stats" (0 points) or "Played No Scoring Events" with no minutes.

- Jun 12: 6 of 11 players scored 0 (mostly Did Not Play)
- Jun 13: 3 of 11 players scored 0, including a famous name (Neymar) who did not play
- Jun 14: clean sheets failed but at least all 11 players started and played

**A guaranteed 4-point player (starts + plays 60) beats a "star" player who might score 0 because they're rotated, benched, or injured.**

Before picking ANY player, check `game-board/players.json` for signals like:
- `starter` / `is_starter` field — if true or high probability, prioritize heavily
- recent appearance/minutes data — if a player has low recent minutes, AVOID
- `eligible` must be true

If the data does not clearly indicate a player will start, DO NOT pick them on reputation alone. Prefer a less famous player with clear starting/minutes signals over a star name with uncertain status.

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

1. Pick the best eligible GK who is confirmed/likely to start.
2. Pick the best 4 eligible DEF players who are confirmed/likely to start.
3. Pick the best 4 eligible MID players who are confirmed/likely to start.
4. Pick the best 2 eligible FWD players who are confirmed/likely to start.
5. Stop.

Do not keep selecting players after the slots are full.

**At every step, "confirmed starter" beats "higher ceiling but uncertain status."**

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

Within each position group, prefer in this exact order:

1. **Confirmed starter / high start probability** (most important — avoids the 0-point trap)
2. Likely to play 60+ minutes (avoid known rotation risks, injury doubts)
3. Goal and assist potential for MID and FWD (recent scoring/assist record)
4. Strong team context (favourite in today's fixture)
5. Clean-sheet potential for GK and DEF (treat as a bonus, not the main reason to pick — see below)

A guaranteed starter with modest output beats a star with uncertain status. Validity AND playing time are more important than upside.

## Clean sheets are a bonus, not a guarantee

Even teams that win big (e.g. 7-1) can concede and lose the clean-sheet bonus. Do not pick defenders purely betting on a clean sheet. Pick defenders who:
- Are confirmed starters for a team facing a much weaker opponent
- Have NOT conceded in recent matches

If clean sheet is uncertain, the defender's realistic expected value is the 4-point floor (start + 60 min). Compare that against attacking midfielders/forwards who get goal/assist bonuses (+4 to +16) regardless of clean sheet status — these are often better picks than a 5th defender.

## Stack the dominant team's attack

When one team is a heavy favourite:
- Pick their GK if a clean sheet looks likely (bonus, not guaranteed)
- Pick 2-3 of their confirmed-starting attacking MID/FWD — these score regardless of clean sheet outcome
- Don't over-invest in their defenders purely for clean-sheet hopes

## Risk Play

Risk Play is optional but should be used when confidence is reasonable — do not skip by default.

Use `risk_play: null` only if team points are 0 or below, or if no claim has reasonable confidence.

If choosing Risk Play:

1. Use only claim IDs from `game-board/claim-catalog.json`.
2. Use only match IDs from `game-board/matches.json`.
3. Do not include `bet_points`, `stake`, or `stake_percent`.
4. Prefer Green claims.
5. See the choose-risk-play skill for full claim-selection logic.

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

Also verify: every single one of the 11 players has a clear signal they will start and play today. If any player's starting status is unknown or doubtful, replace them with a confirmed starter from the same position pool, even if that means a "smaller name."

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
  "strategy": "Built a 1-4-4-2 lineup of confirmed starters only, avoiding any player with uncertain playing-time status, and stacked the attacking midfield of today's strongest favourite."
}

## AGGRESSIVE MODE — hard gate on starter confirmation

On Jun 12, 6 of 11 players (St. Clair, Davies, Osorio, Shaffelburg, Choinière, Deko) returned "Did Not Play Or Missing Stats" — a complete zero. This is the single largest point leak observed.

**New hard rule: if a player's starting status cannot be confirmed as likely/probable from the game board data, DO NOT select them — no exceptions, regardless of reputation, team, or potential ceiling.**

If an entire position group for a team looks uncertain (e.g. a team's full midfield has no clear starter signals), pull players from a DIFFERENT team's position group instead, even if that team is less "exciting." A guaranteed 4 points beats a 50/50 shot at 10 points or 0 points — the expected value favors certainty, and certainty compounds across a long tournament.

## AGGRESSIVE MODE — hunt for goal/assist upside aggressively

Once the starter-confirmation gate is satisfied for all 11 slots, actively re-rank MID and FWD candidates by recent goal/assist output, not just "likely to start." A confirmed-starting attacking midfielder with a recent goal or assist (e.g. Kimmich +12, Vinícius +10, Musiala +10, Wirtz +8) is worth pursuing over a confirmed-starting but purely defensive player with a 4-point ceiling.

Among confirmed starters, prioritize:
1. Players with a goal or assist in their last 1-2 matches
2. Players who are designated penalty takers
3. Players from the most dominant attacking team in today's fixtures
