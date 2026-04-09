# PVA Sprint Capacity Planner 2026

**Owner:** Suleman Malik — Scrum Master, Pandora Digital  
**Team:** PVA (Agentforce) · Engage Product Area  
**Live URL:** https://sulemanpandora-lab.github.io/pva-planner/  
**GitHub repo:** https://github.com/sulemanpandora-lab/pva-planner  

---

## What is this?

A web-based sprint capacity planner for the PVA team at Pandora Digital. It replaces the old Excel-based resource plan and gives the whole team a shared, online view of capacity, leave days, and estimated story points per sprint.

---

## How it works

The planner is a single HTML file (`index.html`) hosted on GitHub Pages. All data is stored in a Google Sheet via Google Apps Script — no server, no database, no tokens that expire.

```
User marks leave days in the planner
  → Clicks "Sync" → "Save changes"
    → Google Apps Script writes JSON to Google Sheet (cell A1, "Data" tab)
      → Any team member clicks "Sync" → "Load latest" to see updated data
```

---

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Plain HTML + CSS + JavaScript (single file) |
| Hosting | GitHub Pages (`main` branch) |
| Database | Google Sheets (JSON in cell A1, "Data" tab) |
| API layer | Google Apps Script (Web App deployment) |

---

## Key files

| File | Description |
|---|---|
| `index.html` | The entire application — HTML, CSS, and JS in one file |
| `README.md` | This file |

---

## Data storage

**Google Sheet:** https://docs.google.com/spreadsheets/d/1ip1MPwJ0a5grDHpoNfaA7SEyqfZzl8UXCb7M5APjSks  
**Tab:** `Data`  
**Cell:** `A1` — contains the full JSON payload with all leave data, guests, and deleted members

**Google Apps Script URL:**
```
https://script.google.com/macros/s/AKfycbzKZycM8lxvENB1x3ZQfhOorKkBfkLAKKCQhgzrUDqb1kanXL7dUPdQDastQ5K3gA7V/exec
```

The script accepts two actions via POST:
- `{ action: "save", data: { ... } }` — overwrites A1 with new JSON
- `{ action: "load" }` — returns the JSON from A1

---

## Team structure (hardcoded in index.html)

| Group | Members |
|---|---|
| Agentforce / AI | Vinod Karpurne, Manish Chetani, Twinkle Garg, Jake McCarthy |
| Frontend | Neeraj Upadhyay, Upender Upender, Mohammed |
| Data | Bahri Benguesmia (0.8 FTE), Satyabrata |
| UX | Aren |
| Conversational Expert | Finka |
| QA / QE | Saswata Bhattacharya (0.5 FTE), Lakshmi Ayothi, Shivanand |

To add or remove permanent team members, edit the `BASE_GROUPS` array in `index.html`. Temporary members can be added via the **"+ Guests"** button in the app without touching the code.

---

## Sprint setup

Sprints run from S1 (Jan 2026) to S25 (Dec 2026) in 2-week cycles across Q1–Q4. Defined in the `SPRINTS` array in `index.html`. Each sprint has a start date, end date, and quarter label.

---

## How to use

### Marking leave
1. Open the planner at https://sulemanpandora-lab.github.io/pva-planner/
2. Select the relevant sprint from the navigation bar
3. Click any working day for a team member to toggle it as **OFF**
4. Click **"⟳ Sync"** → **"Save changes"** to push to Google Sheets

### Loading latest data
1. Click **"⟳ Sync"** → **"Load latest"**
2. The planner updates with all data saved by the team

### Adding a guest (borrowed team member)
1. Click **"+ Guests"**
2. Enter name, role, and select which group they belong to
3. They appear with a **GUEST** badge and are included in capacity calculations

### Removing a team member
- Hover over a member's name and click the small **✕** button
- They are hidden from the planner but their data is preserved
- This is useful for members who have left the team temporarily

---

## Capacity model

| Allocation | Percentage |
|---|---|
| Meetings & ceremonies | 10% |
| Feature development | 70% |
| Enhancements & fixes | 10% |
| Jira / unplanned | 10% |

Story point estimates on the **SP breakdown** tab are based on **70% feature allocation** of available working days.

---

## How to update the code

1. Go to https://github.com/sulemanpandora-lab/pva-planner/edit/main/index.html
2. Make your changes
3. Click **"Commit changes"**
4. GitHub Pages updates automatically within ~1 minute

For bigger changes, bring the full `index.html` into a Claude conversation with this README for context.

---

## History

| Version | Change |
|---|---|
| v1 | Initial build with Atlassian/Confluence sync (OAuth token — expired quickly) |
| v2 | Replaced Confluence sync with Google Sheets + Google Apps Script (no expiry) |

---

## If the sync stops working

The most likely cause is that the Google Apps Script deployment settings changed. To fix:

1. Go to https://script.google.com
2. Open **"PVA Planner Sync"**
3. Click **Deploy** → **Manage deployments**
4. Click the pencil icon → make sure **"Who has access"** is set to **"Anyone"**
5. Click **Deploy** → copy the new URL
6. Update `PROXY_URL` in `index.html` (line ~3 of the script section)
7. Commit to GitHub
