---
name: pick-fantasy-xi
description: Select the best 11 eligible players for the Fantasy XI each matchday, maximizing expected points using form, role, and match context.
---

# Pick Fantasy XI

Use this skill when choosing the daily Fantasy XI for the tournament.

## HARD FORMATION RULES — READ FIRST, CHECK LAST

These rules are ABSOLUTE. A lineup that breaks any of these is INVALID and scores 0.

```
EXACTLY 1  goalkeeper   (position = GK)
EXACTLY 3, 4, or 5  defenders   (position = DEF)
EXACTLY 3, 4, or 5  midfielders (position = MID)
EXACTLY 1, 2, or 3  forwards    (position = FWD)
EXACTLY 11 players total
```

**BANNED formations (DO NOT USE):**
- 1 GK / 3 DEF / 2 MID / 5 FWD → INVALID (5 FWD > max 3, 2 MID < min 3)
- 1 GK / 2 DEF / 4 MID / 4 FWD → INVALID (2 DEF < min 3, 4 FWD > max 3)
- 1 GK / 3 DEF / 3 MID / 4 FWD → INVALID (4 FWD > max 3)
- Any lineup with fewer than 3 MID → INVALID
- Any lineup with more than 3 FWD → INVALID

**VALID formations (pick one):**
- 1-4-4-2  (1 GK, 4 DEF, 4 MID, 2 FWD) ← DEFAULT
- 1-4-3-3  (1 GK, 4 DEF, 3 MID, 3 FWD)
- 1-4-5-1  (1 GK, 4 DEF, 5 MID, 1 FWD)
- 1-5-3-2  (1 GK, 5 DEF, 3 MID, 2 FWD)
- 1-5-4-1  (1 GK, 5 DEF, 4 MID, 1 FWD)
- 1-3-5-2  (1 GK, 3 DEF, 5 MID, 2 FWD)
- 1-3-4-3  (1 GK, 3 DEF, 4 MID, 3 FWD)

Use **1-4-4-2** unless there is a strong reason to deviate.

---

## Step 1 — Load the game board

Read `game-board/players.json`. Each player has a `position` field: GK, DEF, MID, or FWD.
Only consider players where `eligible` is `true`.
Read `game-board/matches.json` and `game-board/teams.json` for match context.

---

## Step 2 — Slot-fill procedure (follow in order)

Fill each slot category separately. Never count a player in two categories.

### Slot A: Goalkeeper (fill exactly 1)

Pick the 1 best eligible GK from `game-board/players.json` where `position = GK`.
Prefer the GK whose team is most likely to keep a clean sheet (low goals conceded, strong defence, weaker opponent).

### Slot B: Defenders (fill exactly 3, 4, or 5 — default 4)

Pick only players where `position = DEF`.
Rank by: (1) team likely to keep clean sheet, (2) starter likelihood, (3) minutes played.
Fill 4 slots by default. Add a 5th DEF only if two elite clean-sheet defenders clearly outvalue a forward.
Never go below 3 DEF.

### Slot C: Midfielders (fill exactly 3, 4, or 5 — default 4)

Pick only players where `position = MID`.
Rank by: (1) goal/assist record, (2) starter likelihood, (3) attacking role (attacking MID > defensive MID).
Fill 4 slots by default. **NEVER fill fewer than 3 MID slots.**

### Slot D: Forwards (fill exactly 1, 2, or 3 — default 2)

Fill remaining slots to reach exactly 11 total: `FWD slots = 11 - 1 GK - DEF count - MID count`.
Pick only players where `position = FWD`.
**NEVER fill more than 3 FWD slots.**
If the math produces 0 or a negative number for FWD, increase MID or DEF count by 1 less and try again.

### Slot-count verification before submitting

Count your selections:
- GK count = 1 ✅ or ❌
- DEF count is 3, 4, or 5 ✅ or ❌
- MID count is 3, 4, or 5 ✅ or ❌
- FWD count is 1, 2, or 3 ✅ or ❌
- Total = 11 ✅ or ❌

If any check is ❌, STOP and rebuild from Step 2. Do not submit an invalid lineup.

---

## Step 3 — Player selection within each slot

Within each position group, rank players by expected points:

**Base points (everyone):** +2 for starting + 2 for 60+ minutes = 4 pts floor if they start.
- Prefer players marked as starters or with high starts-to-appearances ratio.
- Avoid injured, suspended, or rotation-risk players.

**GK/DEF bonus:** +4 clean sheet if team concedes 0. Stack defenders from dominant teams in easy fixtures.

**MID/FWD bonus:** +6 per goal, +4 per assist. Prefer players with recent goal/assist record.

**Penalties:** If a player is the designated penalty taker, increase their priority.

**Avoid:** Players with recent yellow card history (-1 risk), players who regularly sub off before 60 min.

---

## Step 4 — Final check before outputting

Run this checklist:

1. [ ] Exactly 11 player IDs in `fantasy_xi`
2. [ ] All IDs exist in `game-board/players.json` and are eligible today
3. [ ] GK count = 1
4. [ ] DEF count is 3–5
5. [ ] MID count is 3–5
6. [ ] FWD count is 1–3
7. [ ] No duplicate IDs

If any item is unchecked, fix the lineup before returning the JSON answer.

---

## Step 5 — Write the strategy field

1–2 sentences: which teams you drew from, why, and any notable picks.
Example: "Stacked Morocco's defence for a likely clean sheet vs Qatar; added Afif as the primary goal threat. Used 1-4-4-2."
