# Public GitHub Pages Repository

This repository publishes two static pages:

- `index.html`: Genos capability matrix.
- `workflow-projects.html`: public AI project Gantt summary.

The private `genos-workspace` repository maintains the underlying project Markdown (`Workflow Projects/<slug>/README.md`, pool index, and `Workflow Projects/BRIEFING.md`). TH Nano Loan has a separate private `STATUS.md`. These private files are not fetched by this public site and must not be copied here.

## Update Rules

1. Check the local Git status and fetch `origin`. Preserve uncommitted work; use `pull --ff-only` only when a clean branch can be fast-forwarded.
2. Confirm progress and plans against the private project Markdown and any live data source. Do not publish historical tracker references.
3. Manually publish only approved, non-sensitive summaries. Exclude internal URLs, SQL, credentials, PII, unapproved performance numbers, and draft model contracts. Mark future or unverified milestones as planned (`ghost` bars).
4. Every content change to either HTML page also updates that page's top-right `Updated YYYY-MM-DD`. Review the diff, stage only intended files, push to `main`, and check the GitHub Pages build and rendered page.

This is a publication surface, not an issue tracker or the source of truth for project tasks.
