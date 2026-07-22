# How to Add New Test Results

## File to edit
`results-lookup/index.html` — everything is in one file.
Open it in any text editor (VS Code recommended).

---

## Batch codes (Allen result sheets)

| Batch | Allen Batch Code | Test type |
|-------|-----------------|-----------|
| XI-Adv A | `2WAN-16-CA4DP` | JEE Advanced paper |
| XI-Adv B | `2WAN-16-CA4DP` | JEE Advanced paper |
| XI-NEET | `2WMN-16-CA4DP` | NEET paper |
| XI-Mains | `2WJN-16-CA4DP` | JEE Main/KCET paper |

Filter result Excel files by these codes to extract only our DPS students.

---

## Result Excel files (source files)

| File | Tests inside | Relevant batch code |
|------|-------------|---------------------|
| `6021-CENTER-SCHOOL-MEDICAL-NURTURE-FINAL-RESULT-05-05-2026 -DPS.xlsx` | NEET Test 1 (5 May 2026) | `2WMN-16-CA4DP` |
| `6021-MEDICAL-NURTURE-PHASE-I&II-IT-02- (Wd+Mor+Aft)-PROVISIONAL-RESULT_28-06-2026.xlsx` | NEET Test 2 (28 Jun 2026) | `2WMN-16-CA4DP` |
| `6021_JEE MAIN & KCET_NURTURE_IT-02_MOR+AFT_PROVISIONAL_RESULT_28-06-2026.xlsx` | JEE Mains IT-02 (28 Jun 2026) | `2WJN-16-CA4DP` |

Column layout in NEET files: `S.NO | FORM NO | BATCH | CENTER | STUDENT NAME | PHY | CHEM | BOT | ZOO | TOTAL | % | RANK`

Column layout in JEE Mains files: `S.NO | FORM NO | BATCH | CENTER | PAPER | STUDENT NAME | PHY | CHEM | MATHS | TOTAL | % | RANK`

---

## Step 1 — Add a new entry to the `TESTS` array

The `TESTS` array starts around **line 294**. Each entry is one test. Add a new object at the **top** of the array (so the latest test appears first in the app).

### JEE Advanced-style test (XI-Adv A / XI-Adv B)

```js
{ id:"adv-aug-2026", name:"DPS Advanced Test 2", date:"10 Aug 2026", type:"jee",
  batches:{
    "XI-Adv A":{ maxMarks:{Physics:60, Chemistry:60, Math:60, Total:180}, scores:{
      "Student Name":{Physics:30, Chemistry:25, Math:20, Total:75, Rank:45},
    } },
    "XI-Adv B":{ maxMarks:{Physics:60, Chemistry:60, Math:60, Total:180}, scores:{
      "Student Name":{Physics:10, Chemistry:15, Math:5, Total:30, Rank:200},
    } },
  }
},
```

### JEE Mains-style test (XI-Mains) — max marks /180 (60+60+60)

```js
{ id:"jee-mains-aug-2026", name:"JEE Mains Internal Test 3", date:"10 Aug 2026", type:"jee",
  batches:{
    "XI-Mains":{ maxMarks:{Physics:60, Chemistry:60, Math:60, Total:180}, scores:{
      "Student Name":{Physics:49, Chemistry:53, Math:52, Total:154, Rank:9},
    } },
  }
},
```

### NEET-style test (XI-NEET) — max marks /720 (180 each subject, Botany + Zoology separate)

```js
{ id:"neet-aug-2026", name:"NEET Internal Test 3", date:"10 Aug 2026", type:"neet",
  batches:{
    "XI-NEET":{ maxMarks:{Physics:180, Chemistry:180, Botany:180, Zoology:180, Total:720}, scores:{
      "Student Name":{Physics:120, Chemistry:100, Botany:160, Zoology:140, Total:520, Rank:15},
    } },
  }
},
```

### Rules
- `id` must be unique (use kebab-case, e.g. `neet-aug-2026`)
- `date` format: `"5 Jul 2026"` (day, 3-letter month, 4-digit year)
- `Total` in scores must equal the sum of subject scores
- `Rank` is the All India / internal rank from the Allen result sheet
- Students who were **absent** should be **omitted** from `scores` entirely — the app shows "ABSENT" automatically
- Only include batches that actually sat the test

---

## Step 2 — Update the banner test count

Search for this line near the top of the HTML:

```html
<div class="banner-progress"><span>6</span><span> Tests</span></div>
```

Increment the number by however many new tests you added.

---

## Step 3 — Update student merit order (if needed)

After adding a new test, re-sort the `STUDENTS` array for that batch in merit order based on the latest test. The batch rank shown in the app (#1, #2…) is the student's position in `STUDENTS`.

---

## Step 4 — Deploy

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
| XI-Adv A | 34 | Merit order (latest = DPS Advanced Test, 5 Jul 2026) |
| XI-Adv B | 30 | Merit order (latest = DPS Advanced Test, 5 Jul 2026) |
| XI-Mains | 25 | Merit order (latest = JEE Mains IT-02, 28 Jun 2026); absent students at end |
| XI-NEET | 25 | Merit order (latest = NEET Test 2, 28 Jun 2026); absent students at end |
| Grade X | 6 | Alphabetical |
| Grade IX | 18 | Alphabetical |

---

## If a student changes batch

1. Move their name in the `STUDENTS` object to the correct batch (in merit position)
2. In every existing test under `TESTS`, move their score entry from the old batch key to the new batch key
3. Update the Excel merit list (`DPS XI Adv Merit List.xlsx`) to match

---

## Attendance integration

Attendance is pulled **live** from Google Sheets via Apps Script when a student profile is opened. No changes needed here when adding results.

- Apps Script URL: stored as `ATTENDANCE_URL` constant in `index.html`
- Endpoint: `?action=student&name=...&batch=...`
- If a student's name in the Sheet doesn't exactly match the name in `STUDENTS`, attendance shows blank — check spelling carefully

---

## All tests (as of Jul 2026)

| id | Name | Date | Batches | Max Marks |
|----|------|------|---------|-----------|
| `jee-main-jul-2026` | JEE MAIN Internal Test 2 | 19 Jul 2026 | XI-Adv A, XI-Adv B | /300 (100+100+100) |
| `adv-jul-2026` | DPS Advanced Test | 5 Jul 2026 | XI-Adv A, XI-Adv B | /180 (60+60+60) |
| `jee-main-jun-2026` | JEE MAIN Internal Test 1 | 28 Jun 2026 | XI-Adv A, XI-Adv B | /300 (100+100+100) |
| `jee-mains-nurture-jun-2026` | JEE Mains Internal Test 2 | 28 Jun 2026 | XI-Mains | /180 (60+60+60) |
| `neet-jun-2026` | NEET Internal Test 2 | 28 Jun 2026 | XI-NEET | /720 (180×4) |
| `bridge-jun-2026` | Bridge Course Test | Jun 2026 | XI-Adv A, XI-Adv B, XI-NEET, Grade X, Grade IX | varies |
| `neet-may-2026` | NEET Internal Test 1 | 5 May 2026 | XI-NEET | /720 (180×4) |

Note: XI-Adv A/B and XI-Mains took different JEE Main papers on 28 Jun 2026 — /300 and /180 respectively. These are separate test entries.
