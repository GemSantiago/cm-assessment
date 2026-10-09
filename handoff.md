# Project Handoff — RM Assessment Tool
**IBM Consulting FutureNow Center Philippines · Quality Management System Team**
**Session date:** August 7, 2026
**Prepared by:** IBM Bob (AI Assistant)

---

## 1. Project Overview

Built a fully self-contained, browser-based learner assessment tool for the **Records Management Policy and Process Training**, along with a supporting Excel answer key. All content is grounded in two source documents:

| File | Description |
|---|---|
| `2026 Records Management Training_FNC.pptx` | 31-slide training deck (Slides 1–31) |
| `Records Management Policy and Requirements.docx` | Version 7.0 policy document |

---

## 2. Deliverables

### `RM_Assessment.html`
Single standalone HTML file. No external frameworks, no server required — opens directly in any browser.

**Features:**
- Learner must enter their full name before starting
- 15-question pool; 10 questions randomly selected per attempt
- Question order and multiple-choice answer order are randomized each attempt
- Question types: Multiple Choice, True or False, Fill-in-the-Blank, Scenario (Multiple Choice)
- Passing score: **9 out of 10 (85%)**
- No limit on retakes; each retake re-randomizes
- Results page shows:
  - Learner name
  - Date and time of completion (auto-populated)
  - Score and PASSED / FAILED banner
  - All 10 questions answered, with the learner's selected answer shown
  - Incorrect questions flagged with a red **✗ Incorrect** badge and red border
  - Correct answers are **never revealed**
  - On passing: italic note — *"You may keep a screenshot of this results page as your artifact of completion."*
  - Retake button resets and re-randomizes

### `RM_Question_Pool_v2.xlsx`
Excel answer key for facilitator/QMS reference.

**Columns:** `#` · `Question Type` · `Source Reference` · `Question` · `Choice A` · `Choice B` · `Choice C` · `Choice D` · `Correct Answer` · `Notes`

- Navy header row, alternating row shading, Correct Answer column highlighted green
- AutoFilter enabled on all columns
- 15 rows — one per question

---

## 3. Question Pool Summary

| # | Type | Source | Question (summary) | Correct Answer |
|---|---|---|---|---|
| 1 | Multiple Choice | Slide 21 + Policy §4.1 | Role and responsibility of a Records Owner | D – All of the above |
| 2 | Multiple Choice | Slide 22 + Policy §4.1 | Records Custodian primary responsibility | B – Organizing, labeling/indexing, storage, retrieval, and disposal |
| 3 | Multiple Choice | Slide 23 + Slide 9 + Policy §5.2.D | Contents of an accurate label | B – Record code, Filename, Coverage date, Computed end of retention, Data Classification |
| 4 | Multiple Choice | Slide 24 + Policy §3.1 | Meaning of retention code S+01 | C – Superseded + 1 year |
| 5 | True or False | Slide 25 + Policy §5.3 | Records Matrix can only be edited in March | **False** |
| 6 | Multiple Choice | Policy §3.1.B | Type of information with no defined retention period | B – Reference Information |
| 7 | Multiple Choice | Policy §3.3 | Definition of a Document Retention Order (DRO) | B – Notice from IBM Legal pausing WRM retention rules |
| 8 | True or False | Policy §5.2.B | Client-owned records can go offsite at manager's discretion | **False** |
| 9 | Multiple Choice | Slide 19 + Policy §5.3 | Where are Records Matrices stored and who manages them | B – Digital Controls Center (monday.com), Business Controls Team |
| 10 | Fill in the Blank | Policy §5.3 + §6.1 | Annual review deadline for Records Matrices | **March 31** |
| 11 | True or False | Policy §2.2 | Retention determined by content regardless of storage location | **True** |
| 12 | Multiple Choice | Policy §5.4.A | How must confidential information be disposed | B – IBM Security guidelines + local RESO rules (LEG 116) |
| 13 | Scenario (MC) | Policy §5.2.B + Slide 13 | How to retrieve a physically archived offsite record | B – Submit retrieval request through GRE Helpdesk |
| 14 | Scenario (MC) | Policy §5.2.D + Slide 9 | Mislabelled records boxes found during spot check | B – Update labels with all required fields |
| 15 | Scenario (MC) | Slide 17 + Policy §1.4 | Where to find record codes not listed in Records Matrix | B – Worldwide Records Management (WRM) w3 page |

---

## 4. Issues Encountered and How They Were Resolved

### Issue 1 — PowerPoint could not be read directly
**Problem:** The `read_file` tool does not support `.pptx` files.
**Resolution:** Extracted the PPTX as a ZIP archive using PowerShell, parsed each `slide*.xml` file using the OpenXML namespace, and concatenated all paragraph text into a readable flat text file. All 31 slides were successfully extracted.

---

### Issue 2 — First Excel file (`RM_Question_Pool.xlsx`) was corrupt on open
**Problem:** Excel displayed a repair prompt: *"Replaced Part: /xl/worksheets/sheet1.xml — Load error. Line 8, column 0."* The hand-built OpenXML ZIP had a malformed `<autoFilter>` reference in the sheet XML.
**Resolution:** Switched from the pure-Node manual ZIP/OpenXML approach to Python's `openpyxl` library (v3.1.5), which produces fully spec-compliant XML. The new file (`RM_Question_Pool_v2.xlsx`) opens in Excel with no errors.

---

### Issue 3 — Excel file locked when trying to regenerate
**Problem:** `PermissionError: [Errno 13] Permission denied: 'RM_Question_Pool.xlsx'` — the file was open in Excel while the script tried to overwrite it.
**Resolution:** Saved output under a new filename (`RM_Question_Pool_v2.xlsx`) while the original remained open.

---

### Issue 4 — Q14 scenario (departing employee) flagged as incorrect situation
**Problem:** The user indicated the scenario about a departing employee's records was not realistic or applicable.
**Resolution:** Replaced with a mislabelled records box scenario drawn from Policy §5.2.D and Slide 9 — a spot check where a Team Lead finds boxes missing coverage dates and retention period labels.

---

### Issue 5 — Q15 (DRO scenarios) not applicable to learners
**Problem:** Two scenario questions (Q14 and Q15 at the time) referenced Document Retention Orders, which the user confirmed are not commonly applicable to the learner audience.
**Resolution:** Replaced both with:
- A mislabelled records box scenario (Q14 — §5.2.D)
- A scenario about where to find record codes and retention periods (Q15 — Slide 17)

Multiple further iterations were made on Q15 specifically:
1. First replaced with a Shared Services Records Matrix approver scenario → rejected as potentially confusing
2. Replaced with an electronic records labelling scenario → rejected as not the right angle
3. Final version: scenario asking which w3 resource to visit for record codes — answer is the **Worldwide Records Management (WRM) w3 page** (Slide 17 + Policy §1.4) ✓

---

### Issue 6 — Q9 (DCC repository) was fill-in-the-blank
**Problem:** User requested it be changed to multiple choice for consistency and to reduce ambiguity in manual grading.
**Resolution:** Converted Q9 from `fitb` type to `mc` type with four plausible choices (Cobee Lite, DCC, IBM WRM portal, SharePoint). Correct answer: Digital Controls Center (monday.com board), managed by the Business Controls Team.

---

## 5. Files in Project Folder

| File | Purpose |
|---|---|
| `RM_Assessment.html` | **Primary deliverable** — learner assessment tool |
| `RM_Question_Pool_v2.xlsx` | Facilitator answer key |
| `2026 Records Management Training_FNC.pptx` | Source material (training slides) |
| `Records Management Policy and Requirements.docx` | Source material (policy document, v7.0) |
| `.bob/tmp/gen-question-pool.py` | Python script used to generate the Excel file |
| `.bob/tmp/gen-question-pool.mjs` | Earlier Node.js attempt (superseded — do not use) |

---

## 6. Known Considerations

| Item | Detail |
|---|---|
| Fill-in-the-blank (Q10) | Accepts `March 31`, `March 31st`, `31 March`, `31st of March` — case-insensitive. No other variations are accepted; graded automatically. |
| Answer key in page source | The correct answers are embedded in the HTML's JavaScript `POOL` array and are readable via browser DevTools. This is inherent to any client-side-only assessment and is acceptable for internal training use. |
| Email / completion tracking | Not implemented. The learner is instructed to keep a screenshot of the passing results page as their artifact of completion. |
| Retake limit | None — by design. Each retake draws a fresh random 10-question set. |

---

*End of handoff document.*
