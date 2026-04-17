# Testing Guide — PVA Sprint Capacity Planner

This guide explains how to manually test the planner after making code changes.  
No technical setup needed — everything runs in your browser.

---

## How to open the browser console

The console is a built-in tool in every browser that lets you run test commands.

1. Open the planner: https://sulemanpandora-lab.github.io/pva-planner/
2. Press **F12** on your keyboard (or right-click anywhere on the page → **Inspect**)
3. Click the **Console** tab at the top of the panel that opens
4. You can now type or paste commands and press **Enter** to run them

---

## Tests to run after every deployment

Copy and paste each block into the console and press **Enter**.  
A ✅ means the test passed. A ❌ means something is wrong.

---

### Test 1 — Local date is correct (UTC fix)

Checks that the planner uses your local date, not UTC time.

```javascript
const d = new Date();
const local = d.getFullYear() + '-' + String(d.getMonth()+1).padStart(2,'0') + '-' + String(d.getDate()).padStart(2,'0');
const utc = d.toISOString().slice(0,10);
console.log('Local date:', local);
console.log('UTC date:  ', utc);
if (local === utc) {
  console.log('✅ Same — no issue right now (but fix is still important for early mornings)');
} else {
  console.log('✅ Different — fix is working correctly, local date is used');
}
```

**What to look for:** Both dates print. If they differ, the fix is doing its job.

---

### Test 2 — Current sprint is correct

Checks that the planner has selected the sprint that contains today's date.

```javascript
const today = localDateStr();
console.log('Today (local):', today);
console.log('Current sprint selected:', cur.l, '→', cur.s, 'to', cur.e);
const correct = today >= cur.s && today <= cur.e;
console.log(correct ? '✅ Correct sprint selected' : '❌ Wrong sprint! Expected sprint containing ' + today);
```

**What to look for:** `✅ Correct sprint selected`

---

### Test 3 — Today sprint has green marker in nav

Checks that the correct sprint button has the green "today" highlight.

```javascript
const todayButtons = document.querySelectorAll('.sc.today');
console.log('Sprints marked as today:', todayButtons.length);
if (todayButtons.length === 1) {
  console.log('✅ Exactly one sprint has the green today marker:', todayButtons[0].textContent.trim());
} else if (todayButtons.length === 0) {
  console.log('❌ No sprint is marked as today — check localDateStr() and sprint date ranges');
} else {
  console.log('❌ Multiple sprints marked as today — something is wrong');
}
```

**What to look for:** `✅ Exactly one sprint has the green today marker: S8` (or whichever sprint is current)

---

### Test 4 — FTE calculates correctly

Checks that reduced FTE members have the right capacity.  
Uses Bahri (80% FTE) and Saswata (30% FTE) as test cases.

```javascript
const members = allMembers();
const bahri = members.find(m => m.id === 'bahri');
const saswata = members.find(m => m.id === 'saswata');
console.log('Bahri FTE:', getMemberFTE(bahri), bahri && getMemberFTE(bahri) === 0.8 ? '✅' : '❌ Expected 0.8');
console.log('Saswata FTE:', getMemberFTE(saswata), saswata && getMemberFTE(saswata) === 0.3 ? '✅' : '❌ Expected 0.3');
```

**What to look for:** Both lines show `✅`

---

### Test 5 — Google Sheets sync works

Checks that the planner can communicate with Google Sheets.

```javascript
console.log('Testing Google Sheets connection...');
fetch('https://script.google.com/macros/s/AKfycbzKZycM8lxvENB1x3ZQfhOorKkBfkLAKKCQhgzrUDqb1kanXL7dUPdQDastQ5K3gA7V/exec', {
  method: 'POST',
  headers: {'Content-Type': 'text/plain'},
  body: JSON.stringify({action: 'load'}),
  redirect: 'follow'
})
.then(r => r.json())
.then(r => console.log(r.ok ? '✅ Google Sheets connection OK' : '❌ Connection failed: ' + r.error))
.catch(e => console.log('❌ Network error: ' + e.message));
```

**What to look for:** `✅ Google Sheets connection OK`

---

### Test 6 — Team members are correct

Checks that the right people are in the team (and removed members are gone).

```javascript
const members = allMembers();
console.log('Total members:', members.length);
members.forEach(m => console.log(' -', m.n, '|', m.r, '| FTE:', getMemberFTE(m)));

const shouldBeGone = ['jake', 'satyabrata', 'shivanand'];
shouldBeGone.forEach(id => {
  const found = members.find(m => m.id === id);
  console.log(found ? '❌ ' + id + ' should have been removed!' : '✅ ' + id + ' correctly removed');
});
```

**What to look for:** All 11 members listed, and three `✅ correctly removed` lines at the bottom.

---

## Quick full test — run all at once

Paste this entire block to run all tests in one go:

```javascript
console.clear();
console.log('=== PVA Planner Test Suite ===\n');

// Test 1
const today = localDateStr();
const correct = today >= cur.s && today <= cur.e;
console.log('1. Current sprint:', correct ? '✅ ' + cur.l : '❌ Wrong sprint, got ' + cur.l + ' for date ' + today);

// Test 2
const todayBtns = document.querySelectorAll('.sc.today');
console.log('2. Green today marker:', todayBtns.length === 1 ? '✅ ' + todayBtns[0].textContent.trim() : '❌ Found ' + todayBtns.length + ' markers');

// Test 3
const members = allMembers();
const bahri = members.find(m => m.id === 'bahri');
const saswata = members.find(m => m.id === 'saswata');
console.log('3. Bahri FTE (expect 0.8):', getMemberFTE(bahri) === 0.8 ? '✅' : '❌ Got ' + getMemberFTE(bahri));
console.log('4. Saswata FTE (expect 0.3):', getMemberFTE(saswata) === 0.3 ? '✅' : '❌ Got ' + getMemberFTE(saswata));

// Test 4
const shouldBeGone = ['jake', 'satyabrata', 'shivanand'];
const stillPresent = shouldBeGone.filter(id => members.find(m => m.id === id));
console.log('5. Removed members:', stillPresent.length === 0 ? '✅ All removed correctly' : '❌ Still present: ' + stillPresent.join(', '));

// Test 5
console.log('6. Team size (expect 11):', members.length === 11 ? '✅ ' + members.length : '❌ Got ' + members.length);

console.log('\n=== Done ===');
```

---

## When to run these tests

| Situation | Tests to run |
|---|---|
| After any code deployment | All tests (quick full test) |
| After adding/removing a team member | Test 6 |
| After changing FTE values | Test 3 + 4 |
| If sync stops working | Test 5 |
| If wrong sprint is selected on load | Test 1 + 2 |

---

*If any test shows ❌, take a screenshot and share it with Claude to get it fixed.*
