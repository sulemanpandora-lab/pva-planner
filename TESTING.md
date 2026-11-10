# Testing Guide

How to check the planner still works after a code change. Covers both the PVA and Care planners — they share no code, but they share most of their failure modes.

---

## 1. The regression suite

A browser-based suite asserts against both planners' real state. **Run it before every push.**

It lives outside both repositories, because it loads *both* apps from the same origin and so belongs to neither:

```
C:\Users\sulma\planner-tests\tests.html
```

Serve the parent folder over localhost and open it:

```
http://localhost:3737/planner-tests/tests.html
```

46 tests, grouped by what they protect:

| Group | What it catches |
| --- | --- |
| Dates | The off-by-one-day bug — leave landing one column too far left |
| Sprint calendar | Overlaps, duplicate IDs, sprints ending before they start, the corrected Q4-2027 dates |
| Previous-sprint lookup | `prevSprintId` across the turn of the year |
| Velocity | Averaging only what was entered; ignoring non-numeric input |
| FTE overrides | An override stored only when it differs from the roster |
| Capacity flag | Members on the roster but out of the capacity maths |
| Disconnected state | The banner appearing, and saving being blocked, when a load failed |
| Sheet concurrency | The revision field, and that sync is off on localhost |

### Two things that make it work

**It reads through the frame's own `eval`.** The planners declare their state with `const`/`let` at top level, which lands in the frame's lexical scope and **not** on `window`. `frame.contentWindow.DEFAULT_SPRINTS` is `undefined`; `frame.contentWindow.eval('DEFAULT_SPRINTS')` is not.

**It refuses to run if either frame has sync enabled.** Otherwise a test run would write to the team's real Google Sheet. This is not theoretical — it has happened twice. `SYNC_ENABLED` is false only on localhost, which is what makes the suite safe.

### Keep it honest

A green suite that cannot go red is worth nothing. After adding tests, break the thing on purpose and confirm the right test fails. Both times this suite has been extended, it was verified that way:

- reintroducing the date bug → two date tests fail immediately
- making `setConnected` set the flag but skip the banner → exactly one test fails, and it is the right one

---

## 2. Manual checks

Things the suite cannot see, because they need a real server or a real browser.

### After any change

1. Open the planner and confirm today's date falls inside the sprint outlined in green
2. Mark a leave day, reload, confirm it is still there
3. Switch between 2026 and 2027 and back

### After a sync or Apps Script change

4. Press **⟳ Load** — status goes green, no error toast
5. Press **Save** — status goes green
6. Open the planner in **two tabs**. Load in both, change something in each, save both. The second should be refused with *"Someone else saved — press Load, then redo this change"*. If it saves silently instead, the Apps Script has not been redeployed.
7. Go offline (devtools → Network → Offline) and reload. The red **"Not connected"** bar should appear, and saving should be refused.

### After an Apps Script change specifically

Editing the script is not enough. **Deploy → Manage deployments → pencil → New version.** Until then `/exec` serves the old code and nothing you changed is live.

---

## 3. If something looks wrong in the data

Google Sheets keeps full version history: File → Version history → See version history → restore → press **⟳ Load** in the planner.

Nothing is lost permanently, including a bad test run.
