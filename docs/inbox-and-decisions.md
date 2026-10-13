# Application tracking and Gmail reconciliation

The **Job Inbox Tracker** is a separate hourly task that reads Gmail and the existing user-submitted application table (privately named `Sheet1`). Its purpose is to reduce missed assessments/interviews and prevent later emails from overwriting stronger existing application evidence.

## Matching a message to an application

Use the strongest trustworthy evidence, in approximate precedence:
1. ATS requisition/job ID or role-specific URL in the saved application's link and email content.
2. Company + exact or near-exact role title, including team, specialization or seniority.
3. Sender domain/body context only when the role is uniquely identifiable.
4. Recruiting thread continuity from a previously confident same-role match.
5. Application date and email time proximity (secondary evidence, never override a conflicting job ID).

A generic sender like Greenhouse, Ashby or Workday is **not** a company/role identifier. If several rows at one employer remain plausible, do not update a guessed row. Flag `Needs Review` and show why.

## Status model

Observed categories include `Applied`, `OA`, `Recruiter Screen`, `Interview`, `Final Interview`, `Waiting`, `Offer`, `Rejected`, `Follow-up`, `Needs Review`, and a `notValid` audit marker. These are application **states**, distinct from an email **event type** such as `Action Required`.

- A delayed application confirmation may add email metadata but **cannot** regress Interview back to Applied.
- After a user completes an OA or interview and is awaiting next steps, `Waiting` may be appropriate when supported by explicit evidence.
- `Offer` and `Rejected` are terminal absent a new explicit contrary event.
- `notValid` means a deliberate reconciliation found no credible matching Gmail evidence; it does **not** prove the employer rejected the application or that the candidate never applied. Later evidence can supersede it.
- Manual updates carry the user's reported state; subsequent older email confirmations must not undo them.

## Incremental + historical paths

**Incremental path:** search for unprocessed recruiting emails excluding `JobTracker/Processed`; classify and match, update the existing Sheet1 row, then label/read/star as appropriate.

**Blank-status reconciliation:** scan **all** Sheet1 rows with application identity fields but missing status; search historical mail including processed/read messages before assigning a state. If multiple rows remain plausible, mark Needs Review, not notValid. A newsletter or incomplete application reminder alone is insufficient to create an application record.

**Gmail side effects:** `JobTracker/Processed` means scanned/handled, not always resolved. Mark handled mail READ. STAR items still needing action or review. Remove tracker-managed stars after confirmed completion when safe. Keep unresolved cases visible without duplicating hourly alerts.

## Partial failure boundaries

| Failure | Correct recovery |
| --- | --- |
| Gmail read fails | No speculative Sheets updates; next hourly run may retry |
| Sheet write fails after mail match | Don't label mail irreversibly processed in a way that hides retry |
| Sheet update persists but Gmail labeling fails | Keep verified Sheet state; retry missing Gmail side effect separately |
| Ambiguous role match | No speculative stage change; flag review with candidate rows |
| Duplicate delayed confirmation | Keep newer stage and avoid redundant notes or alerts |

A successful email classifier does not authorize Gmail replies, applications or external submissions. No automatic new application row may be created from inbound email alone.

## Operational visibility

Each hourly task prompt requires an every-run concise chat summary, including no-change and partial-error runs. This is *prompt policy*, not proof of notification delivery: the task platform's notification settings and actual run records must be checked separately.

Use [synthetic lifecycle events](../examples/lifecycle-events.synthetic.json) to discuss matching and precedence without publishing personal email or application history.
