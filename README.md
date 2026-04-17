# PVA Sprint Capacity Planner 2026

**Pandora Digital · Agentforce · SM: Suleman Malik**

A sprint capacity planning tool for visualising and managing team capacity across all 25 sprints in 2026.

🔗 **Live tool:** https://sulemanpandora-lab.github.io/pva-planner/

---

## Features

### Sprint navigation
- 25 sprints across Q1–Q4 2026, all in 2-week cycles
- Click any sprint (S1–S25) to navigate to it
- The **current sprint** is highlighted in green so you always know where you are
- The **selected sprint** (the one you are viewing) is highlighted in blue
- Sprints with data show a green dot indicator
- Sprints with custom dates are highlighted in amber

### Summary cards
At the top of each sprint view:
- Number of team members
- Number of working days in the sprint
- Capacity percentage (colour-coded green/amber)
- Total available days
- Estimated story points (based on 70% feature allocation)

### Calendar view
- Table showing all team members against every day in the sprint
- Click any working day to mark it as **OFF / leave**
- Click again to clear the mark
- Weekends are automatically excluded and cannot be toggled
- Capacity bar per person changes colour: green → amber → red

### FTE (Full-Time Equivalent)
FTE describes what percentage of a person's working time is dedicated to the team.

- **Default is 100%** — not shown unless changed
- Reduced FTE appears as an amber badge (e.g. `80%` or `30%`)
- Click the badge to change FTE — options available:
  - 30% — approx. 1.5 days per week
  - 50% — half time
  - 80% — four days per week
  - 100% — full time
- FTE changes apply globally across all sprints
- Changes are saved automatically to Google Sheets

**Default FTE per team member:**

| Name | Default FTE |
|---|---|
| Bahri Benguesmia | 80% |
| Saswata Bhattacharya | 30% |
| All others | 100% |

### Plan longer leave
- Mark leave across multiple sprints in one action
- Select a team member and a date range — all working days in the period are marked as OFF
- Preview shows exactly which sprints are affected and how many days
- Full undo support (8-second window after confirming)

### Guests and students
- Add borrowed team members as guests to any group
- Choose a type when adding:
  - **Regular guest** — defaults to 100% FTE
  - **Student (50%)** — automatically set to 50% FTE, shown with a green STUDENT badge
- Remove guests by hovering their name in the calendar and clicking ✕, or via the guest panel
- Full undo support when removing guests

### Custom sprint dates
- Edit the start and end date of any individual sprint (useful for double sprints or shortened sprints)
- Capacity recalculates automatically when dates change
- Modified sprints are shown in amber in the navigation bar
- Reset to the default date with one click

### Sprint notes
- Free-text field per sprint for epics, goals, blockers and release notes
- Auto-saves 600ms after the last keystroke
- Synced via Google Sheets

### SP breakdown (Story Points)
- Story points broken down by role group: Agentforce/SF, Frontend, UX Design, Conversational Expert, QA/QE, Data
- Shows story points per person with leave days indicated
- Capacity allocation model: 10% ceremonies, 70% feature development, 10% enhancements, 10% unplanned

### Quarter view
- Overview of all sprints across Q1–Q4
- Shows capacity percentage, working days, and whether the sprint has notes
- Click any sprint card to jump directly to it

### Release calendar
- Shows relevant platform and PVA releases for each sprint
- Releases going live during the current sprint are highlighted in amber
- Direct link to Pandora's full release calendar in Confluence

### Google Sheets sync
- Data auto-saves to Google Sheets on every change
- Manual **Save** button available in the header
- **⟳ Load** button pulls the latest data from Google Sheets
- Sync status shown in the header with a green/amber/red dot and timestamp
- Data saved includes: leave, guests, deleted members, FTE overrides, custom sprint dates, sprint notes

### Share via URL
- Generate a share link with all current data encoded in the URL
- Useful for sharing a planning snapshot with stakeholders

### Offline resilience
- Data is cached in the browser's localStorage as a fallback
- Works even if Google Sheets is temporarily unavailable

### Dark mode
- Automatically adapts to the system's dark mode setting

### Reset
- **Reset** button clears all leave data
- Notes, custom sprint dates, and guests are preserved

---

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Plain HTML + CSS + JS — single `index.html` file |
| Hosting | GitHub Pages — auto-deploys from main branch |
| Database | Google Sheets — JSON stored in cell A1, tab "Data" |
| API | Google Apps Script — Web App |

---

## Team members

| Group | Name | FTE |
|---|---|---|
| Agentforce / AI | Vinod Karpurne | 100% |
| Agentforce / AI | Manish Chetani | 100% |
| Agentforce / AI | Twinkle Garg | 100% |
| Frontend | Neeraj Upadhyay | 100% |
| Frontend | Upender Upender | 100% |
| Frontend | Mohammed | 100% |
| Data | Bahri Benguesmia | 80% |
| UX | Aren | 100% |
| Conversational Expert | Finka | 100% |
| QA / QE | Saswata Bhattacharya | 30% |
| QA / QE | Lakshmi Ayothi | 100% |

---

## Updating the code

1. Go to https://github.com/sulemanpandora-lab/pva-planner/edit/main/index.html
2. Select all → delete → paste the new content
3. Commit — GitHub Pages updates within ~1 minute

## If sync stops working

1. Go to https://script.google.com and open **PVA Planner Sync**
2. Deploy → Manage deployments → click the pencil icon
3. Confirm **Who has access** is set to **Anyone**
4. Deploy and copy the new URL if it changed
5. Update `PROXY_URL` in `index.html`

---

## Testing after deployment

After every deployment, run a quick sanity check in your browser.

**How to open the console:**
1. Open the planner at https://sulemanpandora-lab.github.io/pva-planner/
2. Press **F12** on your keyboard
3. Click the **Console** tab
4. Paste the test code below and press **Enter**

**Quick full test — paste this into the console:**

```javascript
console.clear();
console.log('=== PVA Planner Test Suite ===\n');

// Test 1 — correct sprint selected
const today = localDateStr();
const correct = today >= cur.s && today <= cur.e;
console.log('1. Current sprint:', correct ? '✅ ' + cur.l : '❌ Wrong sprint, got ' + cur.l + ' for date ' + today);

// Test 2 — green today marker in nav
const todayBtns = document.querySelectorAll('.sc.today');
console.log('2. Green today marker:', todayBtns.length === 1 ? '✅ ' + todayBtns[0].textContent.trim() : '❌ Found ' + todayBtns.length + ' markers');

// Test 3 — FTE values
const members = allMembers();
const bahri = members.find(m => m.id === 'bahri');
const saswata = members.find(m => m.id === 'saswata');
console.log('3. Bahri FTE (expect 0.8):', getMemberFTE(bahri) === 0.8 ? '✅' : '❌ Got ' + getMemberFTE(bahri));
console.log('4. Saswata FTE (expect 0.3):', getMemberFTE(saswata) === 0.3 ? '✅' : '❌ Got ' + getMemberFTE(saswata));

// Test 4 — removed members are gone
const shouldBeGone = ['jake', 'satyabrata', 'shivanand'];
const stillPresent = shouldBeGone.filter(id => members.find(m => m.id === id));
console.log('5. Removed members:', stillPresent.length === 0 ? '✅ All removed correctly' : '❌ Still present: ' + stillPresent.join(', '));

// Test 5 — team size
console.log('6. Team size (expect 11):', members.length === 11 ? '✅ ' + members.length : '❌ Got ' + members.length);

console.log('\n=== Done ===');
```

All lines should show ✅. If any show ❌, take a screenshot and share it to get it fixed.

For the full testing guide see [TESTING.md](TESTING.md).

---

*Maintained by Suleman Malik, Pandora Digital*
