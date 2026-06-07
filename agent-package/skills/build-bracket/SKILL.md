---
name: build-bracket
description: Make bracket predictions for the knockout phase of the World Cup, maximising expected bracket points using team strength, recent form, and tournament context.
---

# Build Bracket

Use this skill when the tournament asks for bracket picks during the knockout phase.

## When This Skill Applies

Bracket play opens when the organizers announce it. Read `game-board/bracket.json` to see the current bracket state, which rounds are open for picks, and which teams are still in the competition.

Only make picks for rounds listed in `bracket.json` and `rules/` as open for prediction.

## Bracket Scoring Reference

| Round | Points for Correct Pick |
|---|---|
| Round of 32 winner | +5 |
| Round of 16 winner | +8 |
| Quarterfinal winner | +12 |
| Semifinal winner | +18 |
| Champion | +30 |

## Step 1 — Load bracket context

Read `game-board/bracket.json` to see all remaining fixtures and the teams still active.
Read `current-standings/` for current tournament standing context if available.

## Step 2 — Rank remaining teams by expected strength

For each remaining team, evaluate:

**Primary signals (most reliable)**
- Goals scored per match this tournament.
- Goals conceded per match this tournament.
- Margin of victory in recent matches (beating by 2+ vs scraping by).
- Whether they had a difficult or easy path to this stage.

**Secondary signals**
- FIFA world ranking or historical World Cup pedigree (use if match metrics are not available).
- Injury and suspension status for key players.

**Red flags (downgrade a team's expected advance)**
- Star player injured or suspended.
- Three consecutive narrow wins with poor underlying stats.
- Team playing extra time or penalties in recent matches (fatigue factor in short-turnaround rounds).

## Step 3 — Pick each round using expected value logic

Bracket points reward correct picks, so prioritise:
- **Later rounds** (semifinal +18, champion +30) over early rounds (+5) when backing a strong team.
- Never force a pick on a weak team just to fill a bracket slot; if two teams are equal, pick the one with better historical performance or home-continent advantage.

**Safe picks (high confidence)**
- A team that has won all group stage matches convincingly.
- A team whose opposition has injury issues or fatigue from extra time.

**Risky picks (only take if evidence is strong)**
- An upset in the Round of 32 — can be worth +5 if a lower-ranked team showed clear superiority.
- Picking a non-traditional winner as champion — only if form data strongly supports it.

## Step 4 — Champion pick strategy

The champion is worth 30 points — the single highest-value prediction. Commit to one team based on:
1. Best attack record this tournament.
2. Best defence record this tournament.
3. No major injury concerns.
4. Favourable draw path to the final.

If two teams are nearly equal, choose the one with the easier projected semi/final path.

## Step 5 — Output

Follow the exact schema in `output-format/` for bracket answers. Use team IDs from `bracket.json`. Never invent team IDs or match IDs.

Include a brief explanation of your champion pick and any notable upset predictions.

## References

See `references/bracket-scoring.md` for the scoring table and pick value analysis.
