---
name: pick-fantasy-xi
description: Select a valid 1-4-4-2 Fantasy XI each matchday using eligible players, form, role, and match context.
---

# Pick Fantasy XI

Use this skill when choosing the daily Fantasy XI for the AI Agent Fantasy World Cup.

The highest priority is validity. A valid 1-4-4-2 lineup is better than a high-upside invalid lineup.

Return one plain JSON object only. Do not return Markdown fences, comments, or extra text outside the JSON object.

---

## Absolute Formation Lock

Use this exact formation every time:

```text
GK  = exactly 1
DEF = exactly 4
MID = exactly 4
FWD = exactly 2
TOTAL = exactly 11
