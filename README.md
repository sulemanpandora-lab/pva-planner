# PVA Sprint Capacity Planner

**Pandora Digital · Agentforce**

A sprint capacity planning tool for the PVA team: everyone marks their own leave, and the planner works out the team's real capacity and story points per sprint.

🔗 **Live:** https://sulemanpandora-lab.github.io/pva-planner/
📘 **How to use it:** see the Confluence page, *PVA — Sprint Capacity Planner* (Online space)

> ⚠️ **Internal tool.** Built for the PVA team at Pandora Digital. Please ask before adapting it for another team.

---

## Features

### Sprint navigation
- 2026 (25 sprints) and 2027 (26 sprints), all 2-week cycles, with a year toggle
- The **current** sprint is outlined in green; the **selected** sprint has a blue background
- A green dot marks sprints with leave entered; a yellow dot marks sprints with custom dates

### Capacity calendar
- Every team member against every day of the sprint
- Click a working day to mark it as leave, click again to clear
- Weekends are excluded automatically
- Per-person capacity bar: green ≥80%, amber 60–80%, red <60%

### Story points
- Estimated SP per sprint, per group and per person
- Configurable via **⚙ Settings**: SP per day (0.5–2.0) and feature allocation % (60–80%)
- Formula: `available days × FTE × SP per day × feature %`

### Velocity tracking
- **Delivered in S*XX*** — actual SP completed, entered by hand from Jira's Velocity Report
- **Avg velocity** — running average of everything entered

The "delivered" card always refers to the **previous** sprint, never the one on screen: the sprint being planned has not finished yet. The average is the reality check on the estimate — if they drift apart, check the roster before changing the formula.

### FTE
Per-person FTE, editable from an amber badge next to the name. Applies across all sprints. Stored as an override only when it differs from the roster default, so the roster stays the source of truth.

### Leave and guests
- **Plan longer leave** — one date range fills every working day across however many sprints it spans, including across the turn of the year. 8-second undo.
- **Guests** — borrowed team members (100% FTE) or students (50%), removable with undo

### Other
- Custom start/end dates per sprint, with capacity recalculated
- Free-text notes per sprint
- Quarter view — every sprint in the year with capacity %
- Share link with the current data encoded as a snapshot
- Dark mode follows the system setting

---

## Saving

**PVA does not save automatically.** Press **Save** in the header, or the change stays in your browser only. (The Care planner behaves differently — it autosaves.)

Data lives in a shared Google Sheet, reached through a Google Apps Script web app. `localStorage` is a per-browser cache, not the source of truth.

### Three things protect the data

| Guard | What it stops |
| --- | --- |
| `LockService` | Two saves interleaving and leaving broken JSON in the sheet |
| Revision check | A save silently overwriting someone else's edit — it is refused instead |
| Empty-sheet rule | A client that never loaded replacing a populated sheet |

The last one matters more than it looks: if the sheet fetch fails, the app holds whatever the browser had — possibly nothing — and saving that would wipe everyone's data. The app also shows a red **"Not connected to the team sheet"** bar and refuses to save while it is up.

**Recovery:** Google Sheets keeps full version history. File → Version history → See version history, restore, then press **⟳ Load** in the planner.

---

## Tech stack

| Layer | Technology |
| --- | --- |
| Frontend | Plain HTML + CSS + JS, one `index.html` |
| Hosting | GitHub Pages, auto-deploys from `main` |
| Storage | Google Sheets — JSON in cell A1, tab `Data` |
| API | Google Apps Script web app |

**Note on storage:** the whole state sits in one cell, which caps at 50,000 characters. Measured at 7,375 (15%) in November 2026 — fine for now. Run `=LEN(A1)` in a spare cell to check; past ~30,000, switch to chunking across rows (the Care planner already does this).

> The Apps Script is **not** interchangeable with the Care one. PVA's uses `output()` rather than `respond()`, reaches the sheet via `openById()` rather than `getActiveSpreadsheet()`, and has an extra `loadEpics` action that pulls epics from Jira. Replacing `doPost` wholesale deletes that.

---

## Team roster

Deliberately not listed here — this repository is public. The current roster lives in `BASE_GROUPS` in `index.html` and, in readable form, on the Confluence page.

---

## Making changes

1. Edit `index.html` and push to `main`. GitHub Pages redeploys in 1–5 minutes.
2. Run the regression suite first — see [TESTING.md](TESTING.md).

### If sync stops working

1. Open the Apps Script project from the Google Sheet: **Extensions → Apps Script**
2. **Deploy → Manage deployments →** pencil icon
3. Check **Who has access**
4. Changing the code is not enough — you must deploy a **New version**, or `/exec` keeps serving the old one

---

*Maintained by Suleman Malik, Pandora Digital*
