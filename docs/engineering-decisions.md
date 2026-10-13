# Engineering decisions and project evolution

This project evolved through several practical prototypes; these are **distinct implementation layers**, not interchangeable deployments.

## Timeline / key stages

1. **Script-first ATS proof of concept.** Built Python/SQLite parsing for seven provider adapters, identity normalization, deterministic classification and file/JSON output. Why: repeatable extraction, lower manual work and less dependence on expensive/free-form AI decisions.
2. **Recurring company/job discovery.** Moved research and monitoring into separately scheduled AI workflows sharing a Google Sheet. Why: persistent cross-run state and direct interaction with a manually editable job tracker.
3. **Batch + visibility redesign.** Full-list scanning did not complete reliably under constrained task execution budgets. The current hourly **Open Jobs** saved prompt uses Tier A/B/C rotation, up to 25 eligible companies per batch, coverage checkpoints and per-company/run diagnostics.
4. **Resume evidence and approval.** Added JD-to-Master Resume/Story Bank matching, selection among specialized resume bases, independently copied Google Docs and attempted verified one-page PDF export. Why: factual relevance without destructive edits or invented claims.
5. **Separate Gmail Inbox Tracker.** Added incremental and historical Gmail reconciliation with existing submitted-application rows, precedence/ambiguity safeguards and action alerts.
6. **Reusable tools.** Kept the Python/SQLite collector, scheduler prompt generator and resume tailoring skill as **three separately usable GitHub repositories**. This fourth repo describes the broader architecture and operational lessons.

## AI versus deterministic code

| Repeatable / deterministic | AI assisted / human judgment |
| --- | --- |
| URL normalization, provider/job ID parsing, SQLite persistence | Discovering worthwhile emerging companies |
| De-duplication and safe state transitions, specific regex/parsers | Interpreting ambiguous JD requirements |
| Schema contracts, batch/checkpoint accounting, guardrail checks | Mapping candidates' *existing* experience to JD priorities |
| Readback verification and acceptance-case invariants | Deciding whether to pursue a role or approve a resume change |

The principle is **bounded assistance**. No evidence supports claiming a completed production MCP agent framework or production custom LLM API infrastructure; the reusable skill and scheduled AI workflow controls are the demonstrated tools. Cost-conscious choices are design rationale, **not measured dollar/token savings**.

## Four tradeoffs worth explaining in interviews

### 1. One Sheet as a shared state store

**Benefit:** minimal deployment, easy user inspection and manual edits, a real source of truth across research, resume and inbox workflows.

**Cost:** no transaction boundaries across Sheet tabs, quotas and race risks; must protect manual statuses with narrow field ownership, stable IDs and re-reads.

### 2. Strict dedup versus repost detection

**Benefit:** prevent the same official ATS requisition from repeatedly appearing as a 'new' job, even when title or score changes.

**Cost:** generic URLs, provider migrations and role aliases can be ambiguous. Use provider namespace + stable job ID first; supplement by normalized URL and corroborating title/location evidence, not blind merges.

### 3. Bounded batches versus full coverage every hour

**Benefit:** higher probability of meaningful attempted coverage before run budget expires, fair rotations and recoverable checkpoints.

**Cost:** no universal statement that every company is checked every hour. To measure freshness, compute coverage over a full A/B/C cycle, track blocked companies and preserve carry-over.

### 4. Optimistic automation versus human control

**Benefit:** quick resume suggestions and inbox triage without applying to the wrong role or overwriting stronger status evidence.

**Cost:** human confirmation is a deliberate gate. Resume tailoring and staged status updates are not automatically proof of submission.

## Reliability story / root cause narrative

**Observed symptom:** long company lists outgrew what a single hourly prompt could honestly finish; some employers were not attempted and official ATS pages were blocked by dynamic rendering, tool/source errors and insufficient evidence.

**Redesign:** cap assignments at 25 companies in one Tier; persist `Coverage State` with current tier/batch; store `Scan Status` for failures and `Scan Runs` for per-run receipts; classify CHECKED/BLOCKED/NOT_ATTEMPTED; never treat blocked retrieval as a closed job; provide concise visible summaries.

**Residual risk:** tool-dependent scan failures still occur, Sheets and notification channels remain non-atomic, and upstream company eligibility task semantics can drift. No claim of 100% success.

See [run logs](reliability-and-observability.md) and [limitations](limitations-and-roadmap.md).
