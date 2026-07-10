# Genos Income Solution

> Genos Income Document Extraction & QC Service  
> 5 Markets (ID/MY/PH/TH/VN) × 3 Pipelines (Extraction / QC / Rule)

## Quick Links

- 📊 [Capability Matrix (GitHub Pages)](https://cedric-chan.github.io/Genos-Income-Solution/)
- 📈 [Core Dashboard](https://datasuite.shopee.io/dashboard/dashboard/28a041f2-edd5-415b-8ebd-526b76140c9c/normal)
- 📋 [Project Tracker (Google Sheets)](https://docs.google.com/spreadsheets/d/14xaD2ouqZlFIDN2VEp5_f5GK6WxvV16lq28tEveezic/edit?gid=1091600755#gid=1091600755)
- 🔗 [Linear Board](https://linear.app/genos-solution-aio/team/GEN/views/issues)

## Project Structure

```
Genos-Income-Solution/
├── index.html                    # GitHub Pages — Capability Matrix
├── README.md
├── .gitignore
├── AGENTS.md                     # Agent instructions
├── Templates/
│   └── biweekly_update_email.md  # Bi-weekly update email template
├── Presentation/
│   └── genos-capability-matrix.html
├── SQL Analysis/                 # Sherlock SQL queries
│   ├── 解析主链路记录/
│   ├── QC记录/
│   └── BankStatement专项/
├── Project Design/               # API docs & design specs
│   ├── bs_must_knows.md
│   └── Genos API Doc.pdf
├── Utils/
│   ├── s3_util.py
│   └── rules/
└── samples/                      # Not in git (PII)
```

## What is Genos?

Genos is the **Income Document Extraction & QC Service** for Shopee Credit Risk. It processes user-submitted income documents (payslips, bank statements, tax forms, EPF, etc.) across 5 Southeast Asian markets.

### Pipeline Architecture

```
User submits file(s)
    │
    ▼
┌─────────────────────────────────┐
│  UC (User Center)                │
│  └─ QC Model (file-level)        │  ← Intercepts invalid files before Risk
│     File Intercept rate per mkt  │
└──────────────┬──────────────────┘
               │ valid files pass
               ▼
┌─────────────────────────────────┐
│  Risk System                     │
│  ├─ Extraction Model             │  ← Parses doc content
│  └─ Screening Automation         │  ← Routes ticket: Reject / Pass / Manual
│     (app-level, per ticket)      │
└─────────────────────────────────┘
```

### Status Definitions

| Status | Meaning |
|---|---|
| Integrated | Model online, downstream system calling it |
| Ready | Model deployed, awaiting downstream integration |
| Development | Model in training / fine-tuning |
| Labeling | Ops labeling in progress |
| Not Started | Planned but not yet begun |
| N/A | Gate not met (QC requires Extraction ≥ Deployed) |

## How to Use This Repo with AI Agents

1. **Update capability matrix**: "update the HTML matrix with latest Linear status"
2. **Send bi-weekly email**: "Review Linear and draft Genos update email"
3. **Check project status**: "Show me the current Genos capability matrix"

See `AGENTS.md` for detailed agent instructions.