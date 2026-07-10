# AGENTS.md — Genos Income Solution

> GitHub Pages: https://cedric-chan.github.io/Genos-Income-Solution/  
> Linear Board: https://linear.app/genos-solution-aio/team/GEN/views/issues  
> Project Tracker (Google Sheets): https://docs.google.com/spreadsheets/d/14xaD2ouqZlFIDN2VEp5_f5GK6WxvV16lq28tEveezic/edit

## Project Overview

Genos is Monee's LLM-powered income document extraction service for credit evaluation, deployed across ID, MY, PH, TH, VN. It takes non-standard payslips, bank statements, tax declarations, etc. and returns structured JSON for downstream risk decisions.

This repository is the **knowledge base** for Genos project management — capability matrix, email templates, SQL analysis, API docs, and utility scripts. The main deliverable is the single-file HTML [Capability Matrix](https://cedric-chan.github.io/Genos-Income-Solution/) that shows Extraction / QC / Screening status across all 5 markets.

**Core team**: Cedric Chen (PM), Linhai Liu, Huangwei, Duren, Mason (Algo/Eng).

## Quick Reference

### Pipeline Architecture (Two Gates)

```
User submits file(s) → UC (User Center)
  ├── QC Model (file-level) → File Intercept rate per market
  │   Gate: requires Extraction ≥ Deployed for that doc type
  │   Output: uc_decision = reject / pending / pass
  └── valid files pass → Risk System
       ├── Extraction Model (parses doc content → structured JSON)
       └── Screening Automation (app-level, per ticket)
           Routes: Reject / Pass / Manual review
```

### Status Definitions

| Status | Color | Meaning |
|---|---|---|
| Integrated | Dark green `#006B3C` | Model online, downstream system calling it |
| Ready | Light green `#66BB6A` | Model deployed, awaiting downstream integration |
| Development | Blue `#1976D2` | Model in training / fine-tuning / code development |
| Labeling | Yellow `#E6A817` | Ops labeling in progress |
| Not Started | Grey `#C4C4C0` | Planned but not yet begun |
| N/A | Dashed | Gate not met (QC requires Extraction ≥ Deployed) |

### Current Capability Snapshot (2026-07-10)

| Market | File Intercept | Tix Screening (Reject/Pass/Manual) | Key Status |
|---|---|---|---|
| 🇮🇩 ID | 44% | 30% / 4% / 66% | Payslip QC+Extr Integrated; BS Ready; ⚠️ UI rebuild |
| 🇲🇾 MY | 15% | 0% / 0% / 100% | All 4 doc types Integrated; SPLX shadow testing |
| 🇵🇭 PH | 0% | 70% / 8% / 22% | QC models in Dev; Screening live with ~78% automation |
| 🇹🇭 TH | 8% | 87% / 6% / 7% | BS Integrated; Tax in Dev; High screening automation |
| 🇻🇳 VN | 0% | 0% / 0% / 100% | Extraction live; QC & Screening not yet started |

## Directory Structure

```
workspace/
├── index.html                    # GitHub Pages — Capability Matrix (auto-generated)
├── Presentation/
│   └── genos-capability-matrix.html  # Source for index.html
├── Templates/
│   └── biweekly_update_email.md  # Bi-weekly update email template
├── Project Design/              # Source of truth for APIs and protocol
│   ├── Genos API Doc.pdf
│   ├── QC Model API Doc.pdf
│   ├── Genos文件接口出入参整理.xlsx
│   └── bs_must_knows.md         # BS-specific status codes & QC mapping
├── Project Presentation/        # Decks for different audiences
├── Next Plan/                   # Decision Model architecture proposal
├── SQL Analysis/
│   ├── QC记录/                  # QC model invocation stats by market
│   ├── 解析主链路记录/           # Full journey: model → RPC → ops ticket
│   └── BankStatement专项/       # BS parse logs + related queries
├── Utils/
│   ├── s3_util.py               # S3 operations (upload/download/list)
│   └── rules/                   # Groovy scripts for Risk Engine features
├── samples/                     # Sample payslips by market (NOT in git — PII)
└── AGENTS.md                    # ← this file
```

## Working with AI Agents

### Updating the Capability Matrix

Tell the agent: "update the HTML matrix with latest Linear status"
The agent will:
1. Query Linear GraphQL for all active issues
2. Map issue statuses to the 6-tier matrix scale
3. Update `index.html` with new status cells and screening ratios
4. Push to `main` → auto-deploys to GitHub Pages

### Sending Bi-Weekly Email

Tell the agent: "Review Linear and draft Genos update email"
The agent will:
1. Query Linear for recent changes
2. Fill the template in `Templates/biweekly_update_email.md`
3. Draft an email matching the tone and length of the original template

### Managing Linear Issues

Tell the agent: "Create issue in Linear for [task]" or "Update [GEN-XX] status to..."
The agent uses:
- MCP `mcp_Linear` for list_issues, update_issue, create_issue
- Linear GraphQL (via curl + API key) for project assignment
- Workflow state UUIDs cached in agent memory

## Critical Protocol Knowledge

### QC Model `qc_code` and `qc_flag` Mapping

Do not change without re-checking against `Project Design/QC Model API Doc.pdf`.

**qc_code definitions** (from API Doc Section 2):
- 0 = success (file decrypted, parsed, QC executed)
- 1 = file invalid/unreadable (e.g., scanned/image PDF → BS status_code 304)
- 2 = file decryption failed (no password or wrong password)
- 3 = parse failed — file type pending support (BS status_code 302). **Note**: NOT in the API Doc Section 2 definition table; only appears in Section 4 as "3（New）". Known documentation gap.
- 4 = QC execution failed — model internal issue (BS status_code 303/305)
- 5 = QC skipped — fallback when model/service has issues

**`qc_flag` only meaningful when `qc_code = 0`**:
- qc_flag = 1 → QC model hit (bad case) → uc_decision = reject
- qc_flag = 0 → QC model pass → uc_decision = pass

**Caller-side uc_decision rules**:
- reject: qc_code=0+qc_flag=1, or qc_code in (1,2)
- pending: qc_code=3
- pass: everything else (qc_code 4, 5, timeout, Aimos code 101/301)

### BS Parse status_code → QC Mapping

From `Project Design/bs_must_knows.md`:
| BS status_code | Maps to | uc_decision |
|---|---|---|
| 0 (success) | qc_code=0, qc_flag=0 | pass |
| 301 (not a BS file) | qc_flag=1 | reject |
| 302 (bank in scope, no template match) | qc_code=3 | pending |
| 303 (template matched, parse failed) | qc_code=4 | pass |
| 304 (unparseable format) | qc_code=1 | reject |
| 305 (page limit exceeded) | qc_code=4 | pass |

### S3 Path Convention

`genos/live/{region}/{app_id}/{file_type}/{YYYYMMDD}/inputs/{filename}`
Bucket: `sg-szfin-genos-all` (endpoint: `pub.s3g.data-infra.shopee.io`)

### SQL Analysis Conventions

- Parameterized templates: `dwd_credit_{region}_income_submission_file_df` — parameterize by `${region}` (id, my, th, ph)
- Always include partition pruning (`grass_date >= 'YYYY-MM-DD'`)
- Presto vs Spark: Presto for `view__data_smd_genos_*` sources; Spark for insert-overwrite ETL

### Ops Review Workflow

1. Pull cases where rules marked "pass" but Ops rejected → CSV
2. Self-review each case, tag observations
3. Group by reject reason + document type
4. Ask Ops for per-case confirmation with specific questions
5. Reconcile standards, categorize into: accepted / automatable / pending
6. Report updated precision

## What NOT to Push to Git

- `samples/` — contains PII (payslip files, user IDs)
- Field-level API docs → already in `Project Design/` PDFs/Excel
- S3 credentials or internal endpoints → already in code
- `.env`, `credentials*`, `.s3cfg`