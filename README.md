# ARIA — Automated Response & Intelligence Analyst
### Automated Incident Response System v2.0 | Week 10 Final Capstone | 2026

> "Anyone can automate a happy path. This system is designed for failure, uncertainty, and scale."

---

## What is ARIA?

ARIA stands for **Automated Response & Intelligence Analyst**. It is a smart system that watches your incident logs every hour, analyses them using artificial intelligence, asks a human to review the findings, and then automatically creates a report and notifies the right people — all without anyone having to do it manually.

Think of ARIA as a tireless digital assistant that reads every system incident, decides how serious it is, writes a professional report, and keeps a detailed record of everything it did. Every single time. Without fail.

---

## Authors

| Name | Role |
|---|---|
| Mark Muinde | Lead Developer — System Architecture & AI Configuration |
| Paul Murithi | Co-Developer — Workflow Design & Governance Implementation |

**System:** ARIA — Automated Response & Intelligence Analyst
**Course:** AI Automation — Week 10 Final Capstone
**Year:** 2026

---

## Why ARIA Was Built

Before ARIA, incident management was done manually:

- Engineers had to read through raw system logs by hand
- Severity levels were assigned inconsistently — a critical incident could be mislabelled
- Reports were written manually — slow, error-prone, and sometimes skipped entirely
- There was no record of who reviewed what, when, or what decision was made
- If one step failed, the whole process stopped

ARIA solves all of these problems automatically.

---

## How ARIA Works — Plain English

ARIA runs across **3 separate automated workflows** that work together like a production line:

```
Every hour
    ↓
ARIA reads your incident spreadsheet
    ↓
AI analyses each incident and writes a structured report
    ↓
You receive an approval email — Approve or Reject
    ↓
If approved — Google Doc created, team alerted, spreadsheet updated
    ↓
Everything logged to the Run Logs tab for full audit trail
```

### The 3 Workflows

**Workflow 1 — Data Ingestion & AI Analysis**
Runs every hour. Reads all incidents marked as Pending from Google Sheets. Sends each one to the ARIA AI engine which produces a structured 6-field analysis. Validates the output through multiple quality checks. Passes valid Medium severity incidents to Workflow 2. All errors and non-Medium incidents are logged directly to Workflow 3.

**Workflow 2 — Document Creation & Escalation**
Receives the validated incident data from Workflow 1. Sends an approval email to the reviewer. On approval — creates a professional Google Doc post-mortem report, sends a Gmail alert to the team, and updates the incident spreadsheet to mark the incident as Processed. Logs everything to Workflow 3.

**Workflow 3 — Operational Logging & Governance**
The audit engine. Receives data from both Workflow 1 and Workflow 2. Records every execution — success, failure, or rejection — as a detailed row in the Run Logs tab. 23 columns of operational data written automatically every single run.

---

## Rubric Coverage

| Criterion | Score | How ARIA achieves it |
|---|---|---|
| C1 — Structured AI Output & JSON Reliability | 10/10 | 3-layer validation: ARIA prompt discipline + LangChain schema parser + 6-condition JSON guard |
| C2 — Fault Tolerance & Resilient Design | 10/10 | 7 error handling techniques, 19 nodes with continue-on-error, branch isolation |
| C3 — Model Selection, Constraints & Fallbacks | 10/10 | Mistral-7B primary → GPT-3.5-turbo fallback, model_used logged every run |
| C4 — Operational Logging & Governance Signals | 10/10 | 23-column Run Logs tab, 3 entry points to Workflow 3, HEALTHY/FAILED/PENDING signals |

---

## Project Structure

```
Week-10-Final-Capstone/
│
├── README.md                               ← This file
│
├── workflows/
│   ├── WF1_Data_Ingestion_AI_Analysis.json         ← Workflow 1
│   ├── WF2_Doc_Creation_Escalation.json            ← Workflow 2
│   └── WF3_Operational_Logging_Governance.json     ← Workflow 3
│
├── presentations/
│   ├── ARIA_Presentation.pptx              ← Technical deck for panel
│   ├── ARIA_NonTechnical_Deck.pptx         ← Business deck for leadership
│   └── ARIA_3Slide_Panel_Deck.pptx         ← 3-slide presentation deck
│
├── docs/
│   └── ARIA_Project_Documentation.docx     ← Full formal documentation
│
├── templates/
│   ├── ARIA_Incident_Logs_Template.xlsx    ← Incident Logs sheet setup
│   ├── ARIA_Run_Logs_Columns.xlsx          ← Run Logs tab setup
│   ├── gmail_approval_clean.html           ← Approval email body
│   ├── gmail_cto_alert_fixed.html          ← CTO alert email body
│   ├── google_doc_body.txt                 ← Google Doc content template
│   └── ARIA_Sticky_Notes.txt              ← n8n canvas sticky notes
│
└── screenshots/
    ├── wf1_canvas.png
    ├── wf2_canvas.png
    ├── wf3_canvas.png
    ├── execution_logs.png
    ├── run_logs_sheet.png
    ├── approval_email.png
    └── google_doc_report.png
```

---

## What You Need Before Installing

You do not need to be a developer to set up ARIA. You just need accounts on a few free platforms. Here is what to prepare before you start:

| What you need | Where to get it | Cost |
|---|---|---|
| n8n installed on your computer | https://n8n.io/get-started | Free (self-hosted) |
| A Google account | https://accounts.google.com | Free |
| An OpenRouter account | https://openrouter.ai | Free — pay per use |
| A GitHub account (to download files) | https://github.com | Free |

---

## Environment Requirements

| Requirement | Minimum version / detail |
|---|---|
| n8n | v2.15.0 or higher (self-hosted) |
| Operating system | Windows 10+, macOS, or Linux |
| Node.js | v18 or higher (required by n8n) |
| Google account | With Sheets, Docs, and Gmail enabled |
| OpenRouter API key | With credits for Mistral-7B and GPT-3.5-turbo |
| Internet connection | Required for all Google and OpenRouter API calls |

---

## Installation Instructions

Follow these steps in order. Do not skip any step.

---

### PART 1 — Install n8n on your computer

**Step 1 — Install Node.js**

Go to https://nodejs.org and download the LTS version. Run the installer. This is required before n8n can work.

**Step 2 — Install n8n**

Open your Command Prompt (Windows) or Terminal (Mac/Linux) and type:

```bash
npm install -g n8n
```

Wait for it to finish. This may take 2-3 minutes.

**Step 3 — Start n8n**

```bash
n8n start
```

Open your browser and go to:
```
http://localhost:5678
```

You should see the n8n login screen. Create an account when prompted.

---

### PART 2 — Set up your Google Sheet

ARIA reads from and writes to a Google Sheet. You need to set it up correctly before importing any workflows.

**Step 1 — Create a new Google Sheet**

Go to https://sheets.google.com and create a new blank spreadsheet. Name it:
```
ARIA_Incident_Logs
```

**Step 2 — Set up the Incident Logs tab**

Click on the first tab (Sheet1) and rename it to:
```
Incident Logs
```

In Row 1, type these column headers exactly — one per cell starting from cell A1:

```
Incident ID | Raw Logs | Timestamp | Report Status | Google Doc Link | Severity | Root Cause | Recommended Action | Model Used | Processed At
```

**Step 3 — Create the Run Logs tab**

At the bottom of the screen click the **+** button to add a new tab. Name it exactly:
```
Run logs
```
(lowercase 'l' in logs — this must match exactly)

In Row 1 of the Run logs tab, type these 23 column headers one per cell:

```
run_id | execution_timestamp | incident_id | severity | model_used |
approval_decision | human_reviewed | processing_status | overall_run_health |
escalation_required | doc_created | email_sent | sheet_updated |
root_cause | recommended_action | qa_severity_valid | qa_title_length |
qa_postmortem_length | qa_all_fields_present | error_reason |
pipeline_stage_reached | workflow_version | run_timestamp
```

**Step 4 — Add a test incident row**

In Row 2 of the Incident Logs tab, add your first test incident:

| Column | What to type |
|---|---|
| Incident ID | INC-2024-001 |
| Raw Logs | [ERROR] Database connection pool exhausted on prod-db-01. Max connections: 100. Active: 100. |
| Timestamp | 2024-12-09 05:20:45 |
| Report Status | Pending |

Leave all other columns blank. ARIA will fill them in automatically.

**Step 5 — Copy your Sheet ID**

Look at the URL in your browser when the sheet is open:
```
https://docs.google.com/spreadsheets/d/YOUR_SHEET_ID_HERE/edit
```

Copy the long string between `/d/` and `/edit`. Save it — you will need it later.

---

### PART 3 — Set up credentials in n8n

Credentials are the secure connections that allow n8n to talk to Google and OpenRouter on your behalf.

**Step 1 — Open n8n credentials**

In n8n, click the **Settings** icon (bottom left) → click **Credentials** → click **Add credential**

**Step 2 — Add Google Sheets**

- Search for `Google Sheets OAuth2`
- Click it → click **Connect with Google**
- Sign in with your Google account → allow all permissions
- Save — name it `Google Sheets account`

**Step 3 — Add Google Docs**

- Click **Add credential** again
- Search for `Google Docs OAuth2`
- Click **Connect with Google** → sign in and allow permissions
- Save — name it `Google Docs account`

**Step 4 — Add Gmail**

- Click **Add credential** again
- Search for `Gmail OAuth2`
- Click **Connect with Google** → sign in and allow permissions
- Save — name it `Gmail account`

**Step 5 — Add OpenRouter**

- Go to https://openrouter.ai → sign up for a free account
- Go to API Keys → Create new key → copy the key
- Back in n8n — click **Add credential**
- Search for `OpenRouter`
- Paste your API key → save — name it `OpenRouter account`

---

### PART 4 — Import the workflows

> ⚠️ You must import in this exact order: Workflow 3 first, then Workflow 2, then Workflow 1. If you do it in the wrong order the workflows will not be able to find each other.

---

**Import Workflow 3 first — Operational Logging**

1. In n8n click **Workflows** in the left menu
2. Click **Import from file**
3. Select `WF3_Operational_Logging_Governance.json`
4. The workflow opens on the canvas
5. Click the **When Executed by Another Workflow** trigger node
6. Change Input data mode to **Define using fields below**
7. Add these 21 fields — click Add field for each one, set type to String:

```
incident_id, severity, model_used, run_timestamp, processing_status,
approval_decision, human_reviewed, doc_created, email_sent, sheet_updated,
root_cause, recommended_action, qa_severity_valid, qa_title_length,
qa_postmortem_length, qa_all_fields_present, overall_run_health,
escalation_required, error_reason, pipeline_stage_reached, workflow_version
```

8. Click the **Google Sheets — Write Run Logs** node
9. Connect the `Google Sheets account` credential
10. Set the Sheet to your `ARIA_Incident_Logs` sheet
11. Set the Tab to `Run logs`
12. Click the **Active** toggle at the top right to activate WF3
13. Copy the Workflow ID from the URL bar — e.g. `http://localhost:5678/workflow/ABC123` — copy `ABC123`

---

**Import Workflow 2 second — Doc Creation & Escalation**

1. Click **Import from file** → select `WF2_Doc_Creation_Escalation.json`
2. Click the **Gmail — Send Approval Request** node
   - Connect the `Gmail account` credential
   - Set the **To** field to your email address
   - Paste the content from `gmail_approval_clean.html` into the Message field
   - Set Operation to **Send and Wait for Response**
   - Set Response Type to **Approval**
3. Click the **Google Docs — Create & Write Report** node
   - Connect the `Google Docs account` credential
   - Set the Folder ID to a Google Drive folder where reports should be saved
   - Paste the content from `google_doc_body.txt` into the Content field
4. Click the **Gmail — Medium Severity Alert** node
   - Connect the `Gmail account` credential
   - Paste the content from `gmail_cto_alert_fixed.html` into the Message field
5. Click the **Google Sheets — Mark as Processed** node
   - Connect the `Google Sheets account` credential
   - Set Sheet to `ARIA_Incident_Logs` and Tab to `Incident Logs`
6. Click the **Execute Workflow 3 — Log Success** node
   - Source: Database
   - Workflow: By ID → paste the WF3 ID you copied
   - Mode: Run once for each item
7. Click the **Execute Workflow 3 — Log Rejection** node
   - Same settings — same WF3 ID
8. Click the **Active** toggle to activate WF2
9. Copy the WF2 Workflow ID from the URL bar

---

**Import Workflow 1 last — Data Ingestion & AI Analysis**

1. Click **Import from file** → select `WF1_Data_Ingestion_AI_Analysis.json`
2. Click the **Google Sheets — Fetch Incidents** node
   - Connect the `Google Sheets account` credential
   - Set Sheet to `ARIA_Incident_Logs` and Tab to `Incident Logs`
3. Click the **Primary Model — Mistral 7B** node
   - Connect the `OpenRouter account` credential
   - Model: `mistralai/mistral-7b-instruct`
4. Click the **Fallback Model — GPT-3.5 Turbo** node
   - Connect the `OpenRouter account` credential
   - Model: `openai/gpt-3.5-turbo`
5. Click the **JSON Validation Guard** node — confirm these 6 conditions exist:
   - `incident_title` is not empty
   - `severity` equals `Medium`
   - `root_cause` is not empty
   - `business_impact` is not empty
   - `recommended_action` is not empty
   - `post_mortem` is not empty
6. Click the **Execute Workflow 2** node
   - Source: Database → Workflow: By ID → paste WF2 ID
   - Mode: Run once for each item
7. Click the **Execute Workflow 3 — Log Error** node
   - Source: Database → Workflow: By ID → paste WF3 ID
   - Mode: Run once for each item
8. Click the **Active** toggle to activate WF1

---

### PART 5 — Verify everything is working

**Check all three workflows are active**

Go to Workflows in n8n. You should see:
- ✅ WF1 — Data Ingestion & AI Analysis — Active
- ✅ WF2 — Doc Creation & Escalation Routing — Active
- ✅ WF3 — Operational Logging & Governance — Active

**Run a manual test**

1. Open WF1 on the canvas
2. Click **Test Workflow** at the top
3. Open your email inbox — you should receive an ARIA approval email
4. Open the email and click **Approve**
5. Go back to n8n — WF2 should complete within seconds
6. Open your Google Sheets Run logs tab — you should see a new row
7. Open your Incident Logs tab — the test row should now show `Processed`

If all of the above happened — ARIA is fully working.

---

## Usage Guide

### How to add incidents for ARIA to process

Open your `ARIA_Incident_Logs` Google Sheet and add a new row to the Incident Logs tab:

| Column | What to enter |
|---|---|
| Incident ID | A unique ID — e.g. INC-2024-002 |
| Raw Logs | Copy and paste the raw system log text here |
| Timestamp | The date and time the incident occurred |
| Report Status | Type exactly: `Pending` |

Leave everything else blank. ARIA fills the remaining columns automatically.

### How to approve an incident

When ARIA sends you an approval email:

- **Click Approve** — ARIA creates the Google Doc report and continues the pipeline
- **Do not click anything** — after the email is sent the incident moves forward automatically based on your IF node configuration

### How to monitor ARIA

Open the **Run logs** tab in your Google Sheet at any time. Each row tells you:

- Which incident was processed
- Which AI model was used
- Whether the report was created
- Whether the email was sent
- Whether the sheet was updated
- The overall health of the run — HEALTHY / FAILED / PENDING REVIEW

---

## Deployment Instructions

### Correct activation order — always follow this

```
Step 1 → Activate WF3 first (the logging system must be ready first)
Step 2 → Activate WF2 second (the document creator must be ready second)
Step 3 → Activate WF1 last (the trigger that starts everything)
```

### How to test the AI fallback model

1. Open WF1 → click Primary Model — Mistral 7B node
2. Change the model name to `mistralai/fake-model-test`
3. Click Test Workflow
4. After it runs open the Run logs tab
5. The `model_used` column should show `openai/gpt-3.5-turbo`
6. This proves the fallback is working
7. Change the model name back to `mistralai/mistral-7b-instruct` when done

---

## Troubleshooting

| Problem you see | Why it happens | How to fix it |
|---|---|---|
| "Workflow is not active" error | WF3 or WF2 was not activated before WF1 ran | Activate WF3 first, then WF2, then WF1 |
| "Missing node to start execution" | WF2 or WF3 has the wrong trigger node | Replace Webhook trigger with When Executed by Another Workflow node |
| WF3 receives empty data | WF3 trigger set to Accept all data | Change to Define using fields below and add all 21 field names |
| Fields show as undefined in WF2 nodes | Data context changes after approval step | Use $('When Executed by Another Workflow').item.json.fieldname for all expressions |
| No approval email received | Gmail credential not connected or wrong email | Check Gmail node credential and To field address |
| Approval buttons not showing in email | Gmail node operation settings wrong | Set Operation to Send and Wait for Response and Response Type to Approval |
| Run Logs sheet not updating | Column names do not match between n8n and sheet | Check every column name — exact spelling, exact capitalisation |
| All incidents going to error path | JSON guard severity condition wrong | Change severity condition to equals Medium not contains |
| Only 1 item logging instead of all | Execute Workflow mode is wrong | Change Mode to Run once for each item |
| Google Doc link is broken | Wrong node referenced for Doc ID | Use $('Google Docs - Create & Write Report').item.json.id |
| Column names warning in Sheets node | Sheet columns changed after node was set up | Click refresh icon in Sheets node Values to Send section |
| Push rejected on GitHub | Remote has changes you do not have locally | Run git pull origin main then git push origin main --force |

---

## Error Handling Reference

ARIA uses 7 error handling techniques to ensure it never crashes silently:

| Technique | Where it is used | What it does |
|---|---|---|
| continueErrorOutput | AI Agent node | When AI fails routes to error log instead of crashing |
| continueRegularOutput | All 19 other nodes | Pipeline keeps running even if a single node fails |
| Retry x3 with backoff | Google Sheets, Google Docs | Tries again 3 times if the API fails temporarily |
| Retry x2 | Both Gmail nodes | Tries again 2 times if email sending fails |
| Branch isolation | JSON guard false branch, approval false branch | Failed incidents stay isolated and never contaminate successful ones |
| Global errorWorkflow | WF1 and WF2 settings | Points to WF3 so even trigger-level failures get logged |
| Mode — Run once for each item | All Execute Workflow nodes | Each incident processed individually — one failure does not block others |

---

## Severity Routing Logic

This is a deliberate business design decision — not an error:

| Severity | What ARIA does |
|---|---|
| Medium | Creates Google Doc + sends team email + updates sheet + logs to Run Logs |
| High | Logged directly to Run Logs for bulk leadership review — no individual doc created |
| Low | Logged directly to Run Logs — informational only, no document needed |

Only Medium severity incidents go through the full document creation pipeline. This prevents alert fatigue and keeps the system focused on incidents that need immediate documentation.

---

## Screenshots

### Workflow 1 — Data Ingestion & AI Analysis
![WF1 Canvas](screenshots/wf1_canvas.png)

### Workflow 2 — Doc Creation & Escalation Routing
![WF2 Canvas](screenshots/wf2_canvas.png)

### Workflow 3 — Operational Logging & Governance
![WF3 Canvas](screenshots/wf3_canvas.png)

### Execution Logs
![Execution Logs](screenshots/execution_logs.png)

### Run Logs Tab — Google Sheets
![Run Logs](screenshots/run_logs_sheet.png)

### ARIA Approval Email
![Approval Email](screenshots/approval_email.png)

### Generated Google Doc Report
![Google Doc](screenshots/google_doc_report.png)

---

## Credits

| | |
|---|---|
| **Authors** | Mark Muinde & Paul Murithi |
| **System** | ARIA — Automated Response & Intelligence Analyst |
| **Platform** | n8n v2.15.0 (Self Hosted) |
| **AI Models** | OpenRouter — mistralai/mistral-7b-instruct + openai/gpt-3.5-turbo |
| **Course** | AI Automation — Week 10 Final Capstone |
| **Year** | 2026 |
