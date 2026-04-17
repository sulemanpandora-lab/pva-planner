# PVA Sprint Capacity Planner 2026

**Pandora Digital · Agentforce · SM: Suleman Malik**

A sprint capacity planning tool for visualising and managing team capacity across all 25 sprints in 2026.

🔗 **Live tool:** https://sulemanpandora-lab.github.io/pva-planner/

> ⚠️ **Internal use only.** This tool is built for the PVA Agentforce team at Pandora Digital. It is not licensed for use by other teams or organisations without explicit permission from Suleman Malik.

---

## Features

### Sprint navigation
- 25 sprints across Q1–Q4 2026, all in 2-week cycles
- Click any sprint (S1–S25) to navigate to it
- The **current sprint** is highlighted in green so you always know where you are
- The **selected sprint** (the one you are viewing) is highlighted in blue
- Sprints with data show a green dot indicator
- Sprints with custom dates are highlighted with a yellow dot

### Summary cards
At the top of each sprint view:
- Number of team members
- Number of working days in the sprint
- Capacity percentage (colour-coded green/amber)
- Total available days
- Estimated story points (based on configurable feature allocation %)

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
- Click the badge to change FTE — options: 30% / 50% / 80% / 100%
- FTE changes apply globally across all sprints
- Changes are saved automatically to Google Sheets

**Default FTE per team member:**

| Name | Default FTE |
|---|---|
| Bahri Benguesmia | 80% |
| Saswata Bhattacharya | 30% |
| All others | 100% |

### SP settings
- Configurable via the **⚙ Settings** button in the header
- Set **feature allocation %** (60% / 65% / 70% / 75% / 80%)
- Set **SP per day** (0.5 / 1.0 / 1.5 / 2.0)
- Settings are saved to Google Sheets and apply across all sprints

### Plan longer leave
- Mark leave across multiple sprints in one action using a date range
- Preview shows exactly which sprints are affected and how many days
- Full undo support (8-second window)

### Guests and students
- Add borrowed team members as guests to any group
- **Regular guest** — defaults to 100% FTE
- **Student (50%)** — automatically set to 50% FTE, shown with a green STUDENT badge
- Full undo support when removing guests

### Custom sprint dates
- Edit the start and end date of any individual sprint
- Capacity recalculates automatically
- Modified sprints shown with a yellow dot in the navigation bar

### Sprint notes
- Free-text field per sprint for epics, goals, blockers and release notes
- Auto-saves and syncs via Google Sheets

### SP breakdown
- Story points broken down by role group
- Shows SP per person with leave days and FTE indicated

### Quarter view
- Overview of all 25 sprints with capacity percentage
- Click any sprint card to jump directly to it

### Release calendar
- Shows platform and PVA releases per sprint
- Go-live releases highlighted in amber

### Google Sheets sync
- Auto-saves on every change
- Manual **Save** and **⟳ Load** buttons in the header
- Sync status shown with a coloured dot and timestamp

### Share via URL
- Generate a snapshot link with all current data encoded

### Offline resilience
- Data cached in localStorage as fallback

### Dark mode
- Automatically adapts to system setting

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

1. Open https://sulemanpandora-lab.github.io/pva-planner/
2. Press **F12** → click **Console**
3. Type `allow pasting` and press Enter if prompted
4. Paste and run:

```javascript
console.clear();
console.log('=== PVA Planner Test Suite ===\n');
const today = localDateStr();
const correct = today >= cur.s && today <= cur.e;
console.log('1. Current sprint:', correct ? '✅ ' + cur.l : '❌ Wrong sprint, got ' + cur.l + ' for date ' + today);
const todayBtns = document.querySelectorAll('.sc.today');
console.log('2. Green today marker:', todayBtns.length === 1 ? '✅ ' + todayBtns[0].textContent.trim() : '❌ Found ' + todayBtns.length + ' markers');
const members = allMembers();
const bahri = members.find(m => m.id === 'bahri');
const saswata = members.find(m => m.id === 'saswata');
console.log('3. Bahri FTE (expect 0.8):', getMemberFTE(bahri) === 0.8 ? '✅' : '❌ Got ' + getMemberFTE(bahri));
console.log('4. Saswata FTE (expect 0.3):', getMemberFTE(saswata) === 0.3 ? '✅' : '❌ Got ' + getMemberFTE(saswata));
const shouldBeGone = ['jake', 'satyabrata', 'shivanand'];
const stillPresent = shouldBeGone.filter(id => members.find(m => m.id === id));
console.log('5. Removed members:', stillPresent.length === 0 ? '✅ All removed correctly' : '❌ Still present: ' + stillPresent.join(', '));
console.log('6. Team size (expect 11):', members.length === 11 ? '✅ ' + members.length : '❌ Got ' + members.length);
console.log('\n=== Done ===');
```

All lines should show ✅. See [TESTING.md](TESTING.md) for the full guide.

---

## License

Built for internal use by the PVA Agentforce team at Pandora Digital. Please contact Suleman Malik before using or adapting this tool.

---

*Maintained by Suleman Malik, Pandora Digital*
