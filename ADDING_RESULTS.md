# How to Add New Test Results

## File to edit
`results-lookup/index.html` — everything is in one file.
Open it in any text editor (VS Code recommended).

---

## Step 1 — Add a new entry to the `TESTS` array

The `TESTS` array starts around **line 294**. Each entry is one test. Add a new object at the **top** of the array (so the latest test appears first in the app).

### JEE-style test (Physics / Chemistry / Math)

```js
{ id:"unique-id-here", name:"Test Display Name", date:"10 Aug 2026", type:"jee",
  batches:{
    "XI-Adv A":{ maxMarks:{Physics:60, Chemistry:60, Math:60, Total:180}, scores:{
      "Student Name":{Physics:30, Chemistry:25, Math:20, Total:75, Rank:45},
      // ... one line per student
    } },
    "XI-Adv B":{ maxMarks:{Physics:60, Chemistry:60, Math:60, Total:180}, scores:{
      "Student Name":{Physics:10, Chemistry:15, Math:5, Total:30, Rank:200},
    } },
    // add other batches if they sat the same test
  }
},
```

### NEET-style test (Physics / Chemistry / Biology)

```js
{ id:"unique-id-here", name:"Test Display Name", date:"10 Aug 2026", type:"neet",
  batches:{
    "XI-NEET":{ maxMarks:{Physics:180, Chemistry:180, Biology:360, Total:720}, scores:{
      "Student Name":{Physics:120, Chemistry:100, Biology:200, Total:420, Rank:5},
    } },
  }
},
```

### Rules
- `id` must be unique across all tests (use kebab-case, e.g. `adv-aug-2026`)
- `date` format: `"5 Jul 2026"` (day, 3-letter month, 4-digit year)
- `Total` in scores must equal the sum of subject scores
- `Rank` is the **All India / internal rank** from the result sheet — used for display only
- Students who were **absent** should be **omitted** from the `scores` object entirely (the app shows "ABSENT" automatically)
- Only include batches that actually sat the test

---

## Step 2 — Update the banner stat (if needed)

Search for this line near the top of the HTML (around line 230):

```html
<div class="banner-progress"><span>3</span><span> Tests</span></div>
<div class="banner-prog-label">113 students · 6 batches</div>
```

Change `3` to the new total number of tests. The student/batch counts only need updating if students are added or removed.

---

## Step 3 — Deploy

```bash
cd "/Users/sankaracharyadutta/Desktop/Teaching/DPS PROGRAM/Results Senior/results-lookup"
git add index.html
git commit -m "Add [test name] results"
git push origin main
```

GitHub Pages updates in ~1 minute. Live at: **https://physics4noobs.github.io/Results/**

---

## Current batches and student order

| Batch | Count | Order |
|-------|-------|-------|
| XI-Adv A | 34 | Merit order (rank 1 = best) |
| XI-Adv B | 30 | Merit order |
| XI-Mains | 25 | Alphabetical |
| XI-NEET | 25 | Alphabetical |
| Grade X | 6 | Alphabetical |
| Grade IX | 18 | Alphabetical |

**XI-Adv A and B are in merit order** — the batch rank shown in the app (#1, #2 …) is simply the student's position in the `STUDENTS` array. If you rearrange batches after a new merit list, update the `STUDENTS` array order to match.

---

## If a student changes batch

1. Move their name in the `STUDENTS` object to the correct batch (in merit position)
2. In every existing test under `TESTS`, move their score entry from the old batch key to the new batch key
3. Update the Excel merit list (`DPS XI Adv Merit List.xlsx`) to match

---

## Attendance integration

Attendance is pulled **live** from Google Sheets via Apps Script when a student profile is opened. No changes needed here when adding results — it just works.

- Apps Script URL: stored as `ATTENDANCE_URL` constant in `index.html`
- Endpoint: `?action=student&name=...&batch=...`
- If a student's name in the Sheets doesn't exactly match the name in `STUDENTS`, attendance will show blank — check spelling carefully

---

## Existing tests (as of Jul 2026)

| id | Name | Date | Batches |
|----|------|------|---------|
| `adv-jul-2026` | DPS Advanced Test | 5 Jul 2026 | XI-Adv A, XI-Adv B |
| `jee-main-jun-2026` | JEE MAIN Internal Test 1 | 28 Jun 2026 | XI-Adv A, XI-Adv B, XI-Mains |
| `bridge-jun-2026` | Bridge Course Test | Jun 2026 | XI-Adv A, XI-Adv B, XI-NEET, Grade X, Grade IX |
