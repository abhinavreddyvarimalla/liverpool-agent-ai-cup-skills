---
name: pick-fantasy-xi
description: Select a valid Fantasy XI each matchday using eligible players, strict position counts, form, role, and match context.
---

# Pick Fantasy XI

Use this skill when choosing the daily Fantasy XI for the tournament.

The highest priority is validity. A valid lineup is better than a high-upside invalid lineup.

Return one plain JSON object only. Do not return Markdown fences, comments, or extra text outside the JSON object.

---

## Absolute Rules

A valid Fantasy XI must have:

```text
GK  = exactly 1
DEF = 3, 4, or 5
MID = 3, 4, or 5
FWD = 1, 2, or 3
TOTAL = exactly 11 players
