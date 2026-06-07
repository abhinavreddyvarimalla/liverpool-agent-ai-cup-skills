---
name: pick-fantasy-xi
description: Select the best 11 eligible players for the Fantasy XI each matchday, maximizing expected points using form, role, and match context.
---

# Pick Fantasy XI

Use this skill when choosing the daily Fantasy XI for the tournament.

## Step-by-Step Process

### Step 1 — Load the game board
Read `game-board/players.json`, `game-board/matches.json`, and `game-board/teams.json`.
Only consider players whose `eligible` field is `true` for today's matchday.

### Step 2 — Score every eligible player using this priority system

Assign a value score to each player using the following logic (higher = better pick):

**Likelihood of earning base points (+2 start, +2 for 60+ min)**
- Prefer players listed as `starter: true` or with a high starts-to-appearances ratio in their metrics.
- Avoid players flagged as injured, suspended, or uncertain starters.
- If no lineup data is available, prefer players who started in their team's last match.

**Goal and assist threat (forwards and attacking midfielders)**
- Award bonus priority to players who have scored or assisted in recent World Cup matches.
- Prefer players from teams ranked high in attack metrics (goals scored, shots on target).
- A forward from a strong team in a weak-opposition match is your best pick.

**Clean sheet value (defenders and goalkeepers)**
- Prefer defenders and GKs from teams with low goals-conceded metrics.
- If a team is a heavy favourite, their backline is high value: +2 start +2 minutes +4 clean sheet = 8 points minimum.
- Pick only ONE goalkeeper. Choose the GK of the team most likely to keep a clean sheet.

**Penalty takers**
- If the player metrics indicate a player takes penalties, increase their forward/midfielder priority, since penalties count as goals.

**Avoid**
- Players with recent yellow card history (risk of -1 points).
- Players who frequently come off before 60 minutes.
- Players from teams in mismatched fixtures where they are likely to rotate or rest starters.

### Step 3 — Build the formation

Use a formation that maximises value from the available player pool. Default to:
- 1 GK
- 4 DEF
- 4 MID
- 2 FWD

If the pool has several high-value forwards available and defenders are weaker, consider 1-4-3-3. If clean sheets are very likely for two strong teams, consider 1-5-3-2 to stack defenders.

**Hard rules (never break these):**
- Exactly 1 goalkeeper.
- 3 to 5 defenders.
- 3 to 5 midfielders.
- 1 to 3 forwards.
- Exactly 11 player IDs total.
- All 11 player IDs must come from `game-board/players.json` for this matchday and be marked eligible.

### Step 4 — Rank and select the top 11

Build a ranked list from Step 2. Fill the formation slots in this order:
1. Best available GK (1 slot).
2. Best available defenders by value score (fill 4 slots; add a 5th only if two elite clean-sheet defenders beat a forward pick).
3. Best available midfielders by value score (fill 3–4 slots; prefer box-to-box or attacking midfielders over deep-lying defensive midfielders).
4. Best available forwards (fill remaining slots up to 11 total).

If a slot cannot be filled with a clearly better option, choose the player with the most starts and the fewest injury flags.

### Step 5 — Final validity check

Before submitting, verify:
- Exactly 11 player IDs selected.
- All IDs appear in `game-board/players.json` for this matchday.
- Positions match the formation rules above.
- No duplicate IDs.

### Step 6 — Write the strategy field

Briefly explain in 1–2 sentences which teams you leaned on, why, and any notable picks. Example: "Loaded defenders from Brazil and France who face weak opposition for likely clean sheets; added Mbappé as the primary goal threat."

## Tiebreaker Rules

When two players are equally valued:
1. Prefer the player from the team more likely to win the match (check `teams.json` strength or standings).
2. Prefer the player with more appearances this tournament.
3. Prefer the player in the position that earns more bonus points (forward > midfielder > defender > GK, unless clean sheet value flips it).

## References

See `references/position-scoring.md` for a quick scoring cheat-sheet.
See `references/formation-guide.md` for formation decision logic.
