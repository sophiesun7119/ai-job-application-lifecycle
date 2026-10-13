# Limitations, runtime drift and practical roadmap

This is a system with real operational imperfections. Keeping the limitations explicit makes it more credible than claiming that scheduled prompts always run successfully.

## Known policy drift (observed in saved prompts)

**1. Company eligibility vocabulary conflict — unresolved**

- Live Companies tab includes `Status` and `Next Eligible Date`. Current Open Jobs saved task expects `Active / Inactive / Cooldown`, with date-aware gating and only narrow due Cooldown→Active updates.
- The **separate daily Company Discovery saved prompt** still describes `Active / Watch / Ignore` and a shorter column model. The old status vocabulary should not be treated as compatible with the new eligibility gate.
- Consequences can include invalid dropdown values, blocked writes or misinterpreted company eligibility. The case study **documents**, not silently fixes, this problem.

**2. Generator defaults differ from deployed runtime**

- The generic [scheduler prompt generator](https://github.com/sophiesun7119/search-job-scheduler-prompt-tool) creates **Companies / Open Jobs / Runs**, uses Active/Watch/Ignore and expects each run to attempt the full active roster.
- Evolved live Open Jobs prompt uses **Companies / Open Jobs / Scan Status / Scan Runs**, Active/Inactive/Cooldown and up to 25 companies in a single Tier batch.
- Copying the generator's default prompt into the current schedule without mapping would be a semantic regression. The generator has a **runtime parity guide**; no automatic migration is claimed.

**3. Notification requirement vs delivery settings**

- All saved task prompts require a visible chat summary, including no-change and partial runs.
- Inspection showed disabled platform notifications. A prompt-level requirement alone does not prove delivery of push/email or even every chat report. Verify actual task outputs separately.

**4. Dynamic/blocked official ATS sites**

- Real Scan Status/Scan Runs include PARTIAL and failure reasons such as DYNAMIC_CONTENT, SOURCE_ERROR and INSUFFICIENT_EVIDENCE.
- The runtime may retry alternate official sources; inaccessible pages must not be misreported as CHECKED or roles as Closed.

**5. Cross-service atomicity**

- Sheet upserts, Gmail labels/reading/stars and chat delivery are independent operations. Mid-run failure can leave partial side effects; readback, idempotent-ish keys and reconciliation reduce but do not eliminate races.

## Roadmap (proposals only; not claims of completion)

| Priority | Improvement | Validation |
| --- | --- | --- |
| P0 | Reconcile Company Discovery status enums and Next Eligible Date semantics **in a separately authorized runtime change** | Existing Active/Inactive/Cooldown cases, future vs due Cooldown, no unauthorized overwrite |
| P0 | Measure the actual success rate and per-tier cycle coverage from deduplicated Scan Runs | Distinguish CHECKED, BLOCKED, NOT_ATTEMPTED, skipped-ineligible |
| P1 | Add synthetic end-to-end replay harness for URL dedup, status protections and Gmail match ambiguity | Repeat mock events without new duplicates or stage regression |
| P1 | Use a small labeled sample to measure Gmail classification/matching precision and unresolved-rate | Manual labels; no access to private email in public harness |
| P1 | Confirm output Doc copy and page-count QA for each tailored resume | Render exported PDF and visually inspect overflow |
| P2 | Evaluate external state store / worker if Sheet concurrency or task budget outgrows personal workflow | Compare operational complexity, correctness and cost |
| P2 | Add dashboard from synthetic or aggregate metrics only | No private applicant data or raw Gmail exports |

**Do not update scheduled tasks, private Google Sheets, Gmail labels or existing personal data as part of public documentation work.**
