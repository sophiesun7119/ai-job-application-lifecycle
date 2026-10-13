# Google Sheets as the state backend

The live system uses a native Google Sheet as **operational state** for human-readable review, recurring scan receipts and application status. This reduces infrastructure needs for a personal tool, but it offers **none of the transactional guarantees** of PostgreSQL, a queue, or a stateful job scheduler.

The table names below are generic/public labels; actual user data, IDs and personal application records stay private.

## Table ownership and semantics

| Tab | Key fields (logical) | Meaning | Writer |
| --- | --- | --- | --- |
| **Companies** | Company, Tier, Category, Remote, Score, Career Page, Status, Next Eligible Date, Notes | Company universe, curation and eligibility | Daily discovery; hourly jobs task only for authorized due Cooldown→Active transition |
| **Open Jobs** | First Seen, Company, Tier, Title, Location/Work Mode, Level, Match, Job Link, Status, Notes | *Prospective* jobs and user review decisions, not submitted applications | Hourly jobs task; human decisions/on-demand review |
| **Scan Status** | Company, Tier, Last Check, Last Status, Failure Count, Last Failure, Reason Notes, Last Counted Run ID | Per-company diagnostic record and lifetime classified failures | Hourly jobs task |
| **Scan Runs** | Run ID, Run Time, Tier, Batch, Scheduled, Checked, Blocked, Not Attempted, New Added, Material Updates, Closed, Run Status, Coverage State | Per-run audit receipt + persistent rotation checkpoint | Hourly jobs task |
| **Sheet1 (Applications)** | date, company, link, Title, application-other-info, status, last-update, email-type, email-subject, gmail-link, action-needed, notes | **Only applications the user actually submitted**, plus recruiting events and review results | Human-confirmed application workflow; hourly Inbox Tracker |

**Important distinction:** an Open Jobs `Status=New` is not `Sheet1.status=Applied`. A resume tailored to a JD doesn't constitute a submission. The inbox tracker **never creates a new application row based on email alone**.

## Job identity / deduplication

Primary identity:

`(ATS provider, company namespace, official requisition ID or UUID)`

Secondary identity:

`(company, normalized role-specific official URL)`

URL normalization removes tracking query parameters and fragments, normalizes host and trailing slash, and **preserves the meaningful job ID path**. Shared company careers homepages do not uniquely identify a requisition. On ambiguous identity, require evidence or user review rather than risking a merged job.

Before an append, re-read the **entire populated historical Open Jobs table**—including New, Interested, Skip, Applied and Closed—to prevent previous decisions being re-added as fresh openings. Re-read identifiers again immediately before insertion where concurrent jobs/human actions may have changed the table. After writing, read back to confirm that the expected unique identities and fields persisted.

## Data integrity / user decisions

- First Seen means first observation by the tracker, **not** the ATS publication date.
- Human-selected job Status and notes must survive re-scans and re-scoring. Don't replace Applied, Skip or Closed with New.
- `Closed` requires strong direct official evidence; a network error, missing search result or 404 alone isn't definitive.
- Do not overwrite manual notes with generic regenerated descriptions.
- Company `Inactive` and future `Cooldown` are eligibility gates. Due Cooldown→Active transitions in the saved hourly job task are the **only narrow company write exception**, after live revalidation. See known cross-task drift in [limitations](limitations-and-roadmap.md).
- Source evidence, validation and side-effect outcomes are differentiated; absent verification is **unknown**, not a false positive.

## Why not a full database?

A Sheets-backed approach makes live company/job/application state inspectable and manually editable. It supports formulas and review annotations without building a separate UI. Trade-offs include concurrent-edit races, weak atomicity, per-request quotas, slow whole-table scans, dropdown/header drift and ambiguous multi-workflow ownership.

Mitigations are **application-level**, not database-enforced: stable job identities, task-scoped write permissions, narrow batch operations, fresh reads and readback, status precedence, idempotent-ish upserts, and explicit PARTIAL status on persistence failure.

## Synthetic record

```text
Open Jobs:
First Seen: 2026-10-01
Company: ExampleCloud
Title: Backend Platform Engineer
Job Link: https://example.com/jobs/demo-4201
Status: Skip
Notes: User decided not to apply
```

A later scan encounters the same ATS job ID and a better location detail. It may update **only** verified material metadata without changing Skip or First Seen. It does **not** create a second row.

See [mock ATS payload](../examples/mock-ats-jobs.synthetic.json).
