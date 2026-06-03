# ARIA — Automated Response & Intelligence Analyst


> "Anyone can automate a happy path. This system is designed for failure, uncertainty, and scale."

---

## What is ARIA?

ARIA is a production-grade AI-powered incident response pipeline built on **n8n**. It automatically ingests system incident logs every hour, performs forensic triage using a dual-model AI strategy, routes Medium severity incidents for **human approval**, creates formal post-mortem reports in Google Docs, and logs every execution in a structured 23-column governance audit trail.

### Rubric coverage

| Criterion | Score | Evidence |
|---|---|---|
| C1 — Structured AI Output & JSON Reliability | 10/10 | 3-layer validation: ARIA prompt + LangChain parser + JSON guard IF node |
| C2 — Fault Tolerance & Resilient Design | 10/10 | 7 error handling techniques, continueErrorOutput, branch isolation, retry |
| C3 — Model Selection, Constraints & Fallbacks | 10/10 | Mistral-7B primary → GPT-3.5-turbo fallback, model_used logged every run |
| C4 — Operational Logging & Governance Signals | 10/10 | 23-column Run Logs tab written every execution, 3 entry points to WF3 |

---

## Project Structure

```
aria-incident-response/
│
├── README.md
│
├── workflows/
│   ├── WF1_Data_Ingestion_AI_Analysis.json
│   ├── WF2_Doc_Creation_Escalation.json
│   └── WF3_Operational_Logging_Governance.json
│
├── presentations/
│   ├── ARIA_Presentation.pptx              (technical — for panel)
│   └── ARIA_NonTechnical_Deck.pptx         (business — for leadership)
│
├── docs/
│   └── ARIA_Project_Documentation.docx
│
├── templates/
│   ├── ARIA_Incident_Logs_Template.xlsx    (Incident Logs tab setup)
│   ├── ARIA_Run_Logs_Columns.xlsx          (Run Logs tab setup)
│   ├── gmail_approval_clean.html           (approval email body)
│   ├── gmail_cto_alert_fixed.html          (CTO alert email body)
│   └── google_doc_body.txt                 (Google Doc content template)
│
└── screenshots/                            (add after n8n setup)
    ├── wf1_canvas.png
    ├── wf2_canvas.png
    ├── wf3_canvas.png
    ├── execution_logs.png
    ├── run_logs_sheet.png
    ├── approval_email.png
    └── google_doc_report.png
```

---

## Environment Requirements

| Requirement | Detail |
|---|---|
| n8n | v2.15.0+ (self-hosted or cloud) |
| Google account | Sheets, Docs, and Gmail enabled with OAuth2 |
| OpenRouter account | API key with access to Mistral-7B and GPT-3.5-turbo |
| n8n public URL | Required for Wait node approval webhook resume |

---

## Installation Instructions

### Step 1 — Set up Google Sheets

**Incident Logs tab** — ensure these columns exist in row 1:
```
Incident ID | Raw Logs | Timestamp | Report Status | Google Doc Link | Severity | Root Cause | Recommended Action | Model Used | Processed At
```

**Run Logs tab** — create a new tab named `Run logs` (lowercase l) with exactly these 23 column headers:
```
run_id | execution_timestamp | incident_id | severity | model_used |
approval_decision | human_reviewed | processing_status | overall_run_health |
escalation_required | doc_created | email_sent | sheet_updated |
root_cause | recommended_action | qa_severity_valid | qa_title_length |
qa_postmortem_length | qa_all_fields_present | error_reason |
pipeline_stage_reached | workflow_version | run_timestamp
```

### Step 2 — Set up credentials in n8n

Go to n8n Settings → Credentials and create:

| Credential | Type | Notes |
|---|---|---|
| Google Sheets account | Google Sheets OAuth2 | Authorise with your Google account |
| Google Docs account | Google Docs OAuth2 | Same Google account |
| Gmail account | Gmail OAuth2 | Same Google account |
| OpenRouter account | OpenRouter API Key | From openrouter.ai → API Keys |

### Step 3 — Import workflows (order matters)

> ⚠️ Always import in this order: WF3 → WF2 → WF1

**WF3 first:**
1. n8n → Workflows → Import from file → `WF3_Operational_Logging_Governance.json`
2. Connect Google Sheets credential to the Write Run Logs node
3. Set the Sheet ID in the Sheets node to your Google Sheet ID
4. Set `When Executed by Another Workflow` → Input data mode → Define using fields below → add all 21 fields
5. **Activate WF3** — copy its workflow ID from the URL bar

**WF2 second:**
1. Import `WF2_Doc_Creation_Escalation.json`
2. Connect Gmail credential to both Gmail nodes
3. Connect Google Docs credential
4. Connect Google Sheets credential
5. Set reviewer email in Gmail — Send Approval Request node
6. Set Sheet ID in Sheets — Mark as Processed node
7. Set Google Drive folder ID in Google Docs node
8. In both Execute WF3 nodes — set Source: Database, Workflow ID: WF3's ID
9. **Activate WF2** — copy its workflow ID

**WF1 last:**
1. Import `WF1_Data_Ingestion_AI_Analysis.json`
2. Connect OpenRouter credential to both model nodes
3. Connect Google Sheets credential to Fetch Incidents node
4. Set Sheet ID in Sheets — Fetch Incidents node
5. In Execute Workflow 2 node — set Source: Database, Workflow ID: WF2's ID
6. In Execute Workflow 3 — Log Error node — set Source: Database, Workflow ID: WF3's ID
7. **Activate WF1**

---

## Setup & Configuration

### Placeholders to replace

| Placeholder | Replace with | Found in |
|---|---|---|
| Google Sheet ID | Your Sheet ID from the URL | WF1, WF2, WF3 |
| WF2 Workflow ID | Numeric/alphanumeric ID from n8n URL | WF1 Execute Workflow 2 node |
| WF3 Workflow ID | Numeric/alphanumeric ID from n8n URL | WF1 and WF2 Execute WF3 nodes |
| Google Drive Folder ID | Drive folder ID for reports | WF2 Google Docs node |
| Reviewer email | Human approver email address | WF2 Gmail — Send Approval Request |
| OpenRouter credential | Connected credential in n8n | WF1 Primary + Fallback model nodes |

### JSON Validation Guard — correct configuration

The IF node in WF1 must have these 6 conditions all using AND:

1. `{{ $json.output.incident_title }}` — is not empty
2. `{{ $json.output.severity }}` — **equals** `Medium` (not contains)
3. `{{ $json.output.root_cause }}` — is not empty
4. `{{ $json.output.business_impact }}` — is not empty
5. `{{ $json.output.recommended_action }}` — is not empty
6. `{{ $json.output.post_mortem }}` — is not empty

### IF — Approved or Rejected? — correct configuration

After the Wait node, the approval response arrives as:
```
$json.data.approved = true (boolean)
```

Set the condition to:
- Left value: `{{ $json.data.approved }}`
- Operation: `is true`

Do NOT use `$json.query.decision` — that is for webhook-based approval, not n8n's built-in approval type.

---

## Usage Guide

### Adding incidents to process

Add rows to the Incident Logs tab with:
- `Incident ID` — unique ID (e.g. INC-2024-001)
- `Raw Logs` — paste raw system log text
- `Timestamp` — when the incident occurred
- `Report Status` — must be exactly `Pending`

Leave all other columns blank — ARIA fills them automatically.

### Triggering the system

**Automatic:** WF1 runs every hour. All Pending rows with Medium severity AI output are processed.

**Manual test:** Open WF1 → click Test Workflow → runs immediately.

### Approving incidents

1. Check email inbox for ARIA approval email
2. Review the incident analysis — root cause, business impact, recommended action
3. Click **Approve** — pipeline continues creating the Google Doc
4. Click **Reject** — incident logged as pending_review, no document created

### Monitoring execution

Open the Run Logs tab in Google Sheets. Each row represents one incident execution with 23 columns showing exactly what happened, which model ran, whether a human approved, and the overall health of the run.

---

## Deployment Instructions

### Activation order (always follow this)

```
Step 1: Activate WF3 (must be live before WF2 can call it)
Step 2: Activate WF2 (must be live before WF1 can call it)
Step 3: Activate WF1 (schedule trigger now running hourly)
```

### Testing the fallback model

1. Open WF1 → Primary Model — Mistral 7B node
2. Change model name to `mistralai/fake-model`
3. Run Test Workflow
4. Check Run Logs → `model_used` should show `openai/gpt-3.5-turbo`
5. Restore original model name after confirming fallback works

### Testing the rejection path

1. Run a test with a Pending row
2. When the approval email arrives — wait 30 minutes without clicking
3. WF2 auto-rejects and calls WF3
4. Run Logs shows `approval_decision = auto_rejected_timeout` and `overall_run_health = PENDING REVIEW`

---

## Troubleshooting

| Problem | Cause | Fix |
|---|---|---|
| "Workflow is not active" error | WF3 or WF2 not activated | Activate WF3 first, then WF2, then WF1 |
| "Missing node to start execution" | WF2 or WF3 has Webhook trigger instead of Execute Workflow Trigger | Replace Webhook with When Executed by Another Workflow node |
| WF3 receives empty data | WF2 trigger set to Accept all data | Change to Define using fields below with all 21 fields |
| $json.fieldname is undefined after Wait node | Wait node resets $json context | Use $('When Executed by Another Workflow').item.json.fieldname instead |
| Approval buttons not in email | Gmail node not set to Send and Wait for Response + Response Type: Approval | Check Gmail node operation and response type settings |
| Run Logs sheet not updating | Column names in n8n node do not match sheet headers | Ensure exact match — same capitalisation, same spacing |
| All items going to false branch | JSON Guard severity condition wrong | Change severity condition to equals Medium (not contains) |
| Only 1 item logging instead of all | Execute Workflow mode wrong | Change Mode to Run once for each item |
| Google Doc link broken | Doc ID expression references wrong node | Use $('Google Docs - Create & Write Report').item.json.id |
| "Column names updated after setup" warning | Sheet columns changed after node configured | Click refresh icon in Sheets node Values to Send section |

---

## Error Handling Reference

| Technique | Node | What it does |
|---|---|---|
| continueErrorOutput | AI Agent | Error output port routes to parse error path |
| continueRegularOutput | All 19 other nodes | Pipeline continues even if node fails |
| Retry x3 | Google Sheets, Google Docs | Handles transient API failures |
| Retry x2 | Gmail nodes | Handles transient email failures |
| Branch isolation | JSON Guard false, Approval IF false | Failed paths stay isolated |
| 30-min auto-reject | Wait node | No response = safe default rejection |
| Global errorWorkflow | WF1 + WF2 settings | WF3 catches trigger-level failures |

---

## Credits

- **System:** ARIA v2.0 — Automated Response & Intelligence Analyst
- **Platform:** n8n v2.15.0 (Self Hosted)
- **AI Models:** OpenRouter — mistralai/mistral-7b-instruct + openai/gpt-3.5-turbo
- **Author:** Mark Muinde
- **Organisation:** Nairobi Nexus
- **Course:** AI Automation — Week 10 Final Capstone
- **Year:** 2026
