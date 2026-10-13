# Fictional end-to-end example

**All names, IDs, messages and dates in this walkthrough are invented.** It illustrates *allowed workflows*, not a production trace.

## 1. Curate company

A daily company discovery reviews engineering relevance and saves fictional `ExampleCloud` with Tier B, official careers URL, company priority and Active status in Companies. Company score means **long-term monitoring value**, not job-match probability. It does not apply for anything.

## 2. Scan official ATS

The hourly job task selects an eligible Tier B batch using saved rotation state. For `ExampleCloud`, it sees:

- official requisition `examplecloud:greenhouse:demo-4201`
- title `Backend Platform Engineer`
- direct role-specific URL `https://example.com/jobs/demo-4201` (**fictional**)
- a location verified as US remote in this mock case

It compares the stable ATS identity to **all historical** Open Jobs rows. The first encounter inserts the role with `First Seen` and `Status=New`; the next occurrence with a tracking parameter is an **upsert/unchanged**, never a second new role. Existing human Skip/Closed/Applied statuses stay intact.

## 3. User reviews fit

The candidate profile favors Python, AWS and PostgreSQL; the JD also asks for Go experience. A JD-to-evidence matrix marks Python/AWS/PostgreSQL as supported only if grounded by actual resume and Story Bank, and Go as an **unknown or gap**, not a fabricated bullet.

The assistant recommends a role and waits for the user's explicit choice. Choosing does not mean applying.

## 4. Resume copy and PDF check

With selected role and access to two Base Resume Docs plus the Story Bank, the assistant recommends the strongest base and presents exact proposed edits. Whole-bullet rewriting or deletion requires approval.

After approval, it creates a **new** Google Doc in the configured folder. It exports and actually renders the PDF to check one page and clipping. If rendering is unavailable, it reports **page count unverified**. The original documents remain unchanged.

## 5. Human application

The candidate opens the official job link and **manually submits**. Only after a confirmation does the tracking workflow record an application in Sheet1 with application date, job link and title. A prepared resume alone never creates an application record.

## 6. Recruiting email arrives

A fictional Greenhouse confirmation arrives. The hourly Inbox Tracker identifies the existing Sheet1 row using the exact role job ID, not just the generic Greenhouse sender.

If the row is already at Interview status, a delayed confirmation cannot regress it to Applied. The tracker can update email metadata and append history without changing the advanced application stage.

Another email requests an online assessment. Matching is unique, so Sheet1 gets `status=OA`, `action-needed=YES`, an appended note and a Gmail link. Gmail is marked read, labeled `JobTracker/Processed`, and starred while action remains outstanding. A user completion update may move the status to Waiting.

If two applications at the same company cannot be disambiguated by job ID/title/date/thread, the tracker marks **Needs Review** rather than corrupting either row.

## 7. Observe a failed scan

On the next hourly batch, a different fictional company is assigned but its ATS is inaccessible. `Scan Status` retains the classified failure and cumulative count; `Scan Runs` records `PARTIAL` and the checkpoint. A later run can retry its coverage position. A 404 or timeout alone never closes prior job records.

## 8. What is not claimed

This example does **not** demonstrate an end-to-end authenticated replay on a public dataset. It does not imply all emails are classified perfectly, all jobs are current, all scheduled runs complete, or external Gmail+Sheets side effects commit atomically.

See the [synthetic events](../examples/lifecycle-events.synthetic.json), [mock ATS jobs](../examples/mock-ats-jobs.synthetic.json) and [mock run receipts](../examples/scan-runs.synthetic.json).
