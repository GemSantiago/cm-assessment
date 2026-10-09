# Assessment Creation & GitHub Pages Deployment Reference Guide
**IBM Consulting FutureNow Center Philippines · Quality Management System Team**

This reference guide provides the exact prompts, parameters, code architecture, and step-by-step instructions needed to create interactive, browser-based training assessments and publish them publicly via GitHub Pages.

---

## Table of Contents
1. [Assessment Generation Prompt Template](#1-assessment-generation-prompt-template)
2. [Facilitator Excel Key Prompt Template](#2-facilitator-excel-key-prompt-template)
3. [Standard Assessment Architecture & Requirements](#3-standard-assessment-architecture--requirements)
4. [Step-by-Step GitHub Repository & Hosting Guide](#4-step-by-step-github-repository--hosting-guide)
5. [Common Troubleshooting & Tips](#5-common-troubleshooting--tips)

---

## 1. Assessment Generation Prompt Template

Copy and fill in the bracketed values `[...]` when requesting a new assessment:

```markdown
I want to create a new interactive browser assessment tool similar to RM_Assessment.html / Change_Management_Assessment.html.

Source Materials:
- [Insert Presentation File Name, e.g., Training_v1.0.pptx]
- [Insert Policy Document File Name, e.g., Policy_Document.docx]

Assessment Parameters:
- Assessment Title: [e.g., Records Management Assessment / Change Management Assessment / Privacy Assessment]
- Target Audience / Org: IBM Consulting FutureNow Center Philippines | Quality Management System Team
- Pool Size: [e.g., 15 questions grounded in source materials]
- Questions per Attempt: [e.g., 10 questions randomly drawn from the pool]
- Passing Score: [e.g., 85% / 9 out of 10]
- Question Distribution: Multiple Choice, True/False, Fill-in-the-Blank, and Real-world Workplace Scenarios.

Please deliver:
1. A table of the extracted question pool with source references and answer keys.
2. A standalone, single-file interactive HTML assessment with:
   - Learner name input validation
   - Random pool sampling & choice shuffling
   - Dynamic progress bar
   - Instant grading
   - Security logic (never reveal correct answers on failure or in the results breakdown)
   - Completion banner with timestamp and screenshot note
   - Unlimited retakes with fresh question re-randomization
3. An index.html entry point for GitHub Pages.
4. An Excel answer key file (.xlsx) for facilitators.
```

---

## 2. Facilitator Excel Key Prompt Template

```markdown
Please generate an Excel answer key file ([Assessment_Name]_Question_Pool.xlsx) for facilitators matching the 15-question pool.

File Structure:
- Sheet Name: "Question Pool"
- Header Styling: Dark Navy background (#001141), bold white text
- Columns:
  1. # (Question Number: 1 to 15)
  2. Question Type (Multiple Choice, True or False, Fill in the Blank, Scenario)
  3. Source Reference (Slide # / Policy §)
  4. Question
  5. Choice A
  6. Choice B
  7. Choice C
  8. Choice D
  9. Correct Answer
  10. Notes / Rationales
```

---

## 3. Standard Assessment Architecture & Requirements

Each standalone HTML file (`index.html` / `[Name]_Assessment.html`) contains embedded CSS and Vanilla JavaScript:

### UI / Styling
- **Typography:** `"IBM Plex Sans", -apple-system, "Segoe UI", system-ui, sans-serif`
- **Palette:**
  - Header: `#001141` (Deep Navy) with `#a6c8ff` subheadings
  - Background: `#f4f4f4`
  - Card: `#ffffff` with `#e5e7eb` borders
  - Buttons: `#0f62fe` (IBM Blue) hover `#0353e9`
  - Passed Banner: `#defbe6` with `#24a148` border & text
  - Failed Banner: `#fff1f1` with `#da1e28` border & text

### Logic & Security Constraints
- **Learner Name Validation:** Prevents quiz start if name input is blank.
- **Random Draw:** Samples $N$ questions (e.g. 10) from the total pool (e.g. 15) using Fisher-Yates shuffle.
- **Choice Shuffling:** Shuffles multiple-choice options dynamically on each render so option positions change per attempt.
- **Security Logic:**
  - When graded, wrong answers display `✗ Incorrect` alongside what the learner selected.
  - **The correct answer is NEVER displayed on failure or in the breakdown**, preserving test integrity for retakes.
- **Completion Timestamp:** Captures `new Date()` (date and time) on submission for screenshot audit artifacts.

---

## 4. Step-by-Step GitHub Repository & Hosting Guide

Follow these steps to host your assessment publicly on GitHub Pages:

### Step 4.1: Ensure `index.html` Exists
GitHub Pages automatically serves `index.html` as the default homepage.
- Keep your primary assessment named as `index.html` in the root folder of your project.

### Step 4.2: Create a New Repository on GitHub
1. Open your browser and go to: **[https://github.com/new](https://github.com/new)**
2. Enter the **Repository name** (e.g., `cm-assessment`, `pd-assessment`, `rm-assessment`).
3. Set visibility to **Public**.
4. **Do NOT check** "Add a README file" (we will push from local).
5. Click **Create repository**.

### Step 4.3: Initialize and Push from Local Workspace (PowerShell)
Open PowerShell in your workspace folder (`c:\Users\GEMSANTIAGO\Documents\Bob\[Your_Project_Folder]`) and run:

```powershell
# 1. Initialize local Git repository
git init -b main

# 2. Stage all deliverables
git add index.html README.md *.html *.xlsx *.docx *.pptx

# 3. Commit files
git commit -m "feat: Initial assessment release"

# 4. Link to your GitHub remote repository (replace with your repo name)
git remote add origin https://github.com/GemSantiago/[REPO-NAME].git

# 5. Push to GitHub
git push -u origin main
```

*(Note: If you have already initialized Git, you only need to run `git add .`, `git commit -m "update"`, and `git push`).*

### Step 4.4: Enable GitHub Pages
1. Navigate to: `https://github.com/GemSantiago/[REPO-NAME]/settings/pages`
2. Under **Build and deployment**:
   - **Source:** Select `Deploy from a branch`
   - **Branch:** Select `main`
   - **Folder:** Select `/ (root)`
3. Click **Save**.

### Step 4.5: Access the Public URL
Your assessment will be live at:
```
https://gemsantiago.github.io/[REPO-NAME]/
```

---

## 5. Common Troubleshooting & Tips

| Issue | Cause | Solution |
|---|---|---|
| **404 Page Not Found right after saving Pages** | GitHub Pages build workflow (`pages-build-deployment`) takes **1 to 2 minutes** on initial setup. | Wait 60–90 seconds, then hard-refresh browser with **Ctrl + F5**. |
| **Old version showing in browser** | Browser cached the old HTML file. | Hard refresh using **Ctrl + F5** (Windows) or open in an Incognito / Private window. |
| **Questions keep same order on retake** | Re-randomization function not triggered. | Ensure `retake()` calls `sampleN(POOL, 10)` and re-runs `shuffle()` before re-rendering. |
| **Excel file corrupt on open** | Malformed OpenXML formatting. | Always use `office_edit` batch operation or standard OpenXML validators. |
| **Remote origin already exists error** | Git remote was previously attached. | Run `git remote set-url origin https://github.com/GemSantiago/[NEW-REPO].git` or check `git remote -v`. |

---
*Created for IBM Consulting FutureNow Center Philippines Quality Management System Team.*
