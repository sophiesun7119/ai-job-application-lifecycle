# Implementation status and evidence map

**Evidence reviewed:** updated private AI-project Career Story Bank (2026-10-12), three public implementation repositories, current saved task prompts for Company Discovery, Open Jobs and Inbox Tracker, and private live Google Sheet metadata / selected headers and historical scan receipts. This public case study includes **no private file copies**.

## What is implemented or documented

| Capability | Current evidence / scope | Confidence |
| --- | --- | --- |
| Python/SQLite ATS collector, seven providers | Public `search-job` source, tests and stage documentation; refresh manually triggered | Publicly inspectable implementation |
| Daily Company Discovery task | Enabled saved automation prompt; writes curated Companies state; uses legacy company status labels | Configured runtime, semantics drift |
| Hourly Open Jobs tier scanning | Enabled saved V5.2 prompt with eligible-company gate, max-25 one-tier batches, full-history dedup, Scan Status / Scan Runs | Configured runtime + real scan receipts |
| Scan receipts/failures/checkpoints | Live Sheet shows COMPLETED and PARTIAL, blocked categories, cumulative failures, serialized coverage snapshots | Live-state evidence, not 100% success |
| On-demand job matching/resume customization | Private project workflow plus public `ai-resume-tailoring-agent` instruction package; requires connected tools/approval | Workflow and artifacts, not a standalone deployed web service |
| Human application submission + Sheet1 | Sheet1 exists with submitted-application fields and status history; manual submission required | Connected state and workflow |
| Hourly Gmail Inbox Tracker | Enabled saved task prompt with processed label, precedence, historical blank-status audit, action alerts | Configured task; metrics not audited |
| Prompt generator | Public `search-job-scheduler-prompt-tool` generates two text prompts | Authoring tool only |
| Every-run chat summaries | Present in saved task prompts; independent task notification flags were **disabled** at inspection | Desired behavior, NOT delivery guarantee |

## Dated, scoped figures

| Figure | What it means | What it does **not** mean |
| --- | --- | --- |
| **145 companies** (2026-10-12) | Distinct nonblank company names in the active connected tracker snapshot, as documented in Story Bank | Currently hiring; eligible to scan; unique external firm identities |
| **512 jobs** (2026-10-12) | Populated Open Jobs records with role-specific links | All remain open; all from one run; verified all on 2026-10-12 |
| **78 seeded / 75 completed board checks / 14,178 deduplicated identities** | Separate local Python/SQLite prototype's documented scan | Combined or equivalent to the Google Sheet's 512 job records |
| **~300 recruiting emails / ~3 weeks** | User-reported approximate handled volume | A certified Gmail metric, evaluation benchmark or proven time saving |

Do not hardcode these as live dashboard values. The collection will keep growing. For portfolio bullets, prefer a dated snapshot or honest lower-bound range.

## Not verified or not implemented as a single deployed platform

- The three source code repos and ChatGPT scheduled tasks are **not** deployed as one containerized service or one unified application with a public installer.
- Scheduled text prompts do not establish that a job actually scanned, wrote to a Sheet or delivered a push alert; read the latest run log and platform notification settings.
- No durable atomic transaction spans Google Sheets, Google Docs and Gmail; do not claim strict exactly-once semantics.
- No proof of complete hourly full-roster coverage; the design intentionally uses tier batches.
- No measured cost saving, inbox precision/recall, resume callback uplift, application throughput improvement or engineer-hours saved.
- PDF one-page success is a **per-document verification requirement**, not a global guarantee.

## Public evidence pointers

- [Local collector README](https://github.com/sophiesun7119/search-job/blob/main/code/README.md)
- [Local collector implementation stage](https://github.com/sophiesun7119/search-job/blob/main/code/STAGE.md)
- [Scheduler template README](https://github.com/sophiesun7119/search-job-scheduler-prompt-tool)
- [Resume tailoring skill entrypoint](https://github.com/sophiesun7119/ai-resume-tailoring-agent/blob/main/SKILL.md)

This page is a **snapshot**, not an automatically refreshed operational dashboard.
