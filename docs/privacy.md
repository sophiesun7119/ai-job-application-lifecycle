# Privacy and open-source presentation

This repository is public. It intentionally contains **architecture, source-linked implementation boundaries, mock job data, synthetic run records and troubleshooting stories**, not private operating data.

## Never publish

- Actual Google Sheet/Doc/Drive IDs or direct private links
- Personal contact/email addresses, real resume and Career Story Bank, candidate scoring preferences, notes or application records
- Gmail sender/recipient identity, subjects, message bodies, threads, deep links, labels tied to private messages
- OAuth tokens, cookie values, API keys, request headers or screenshots exposing credentials
- Raw saved scheduled prompts when they include private workbook URLs, personal career details or runtime identifiers
- Company-specific private notes and exact per-company failure history from a real account

## What is safe to demonstrate

- Fictional employer `ExampleCloud`, fake roles, non-resolving `example.com` URLs
- Synthetic `Scan Runs` and `Scan Status` schemas and constructed CHECKED/BLOCKED/NOT_ATTEMPTED values
- General data ownership and abstract state machines
- Aggregate counts already approved for project narrative, scoped to date/source and without personal row-level details
- Links to separately published public implementation repositories

## Review before every GitHub commit

1. Check the entire diff for private IDs, names, direct resource URLs, personally identifying snippets and authentication data.
2. Avoid publishing raw screenshots of Google Sheets/Gmail; recreate a mock instead.
3. Label every demo/log/example **synthetic** visibly; fake successes are not real metrics.
4. Verify Markdown links to the other three repositories and all local docs.
5. Confirm docs don't claim all three repos are one deployed service, that scheduled notifications are guaranteed or that resumes/applications are automatically submitted.
6. Keep private account credentials and task configs outside Git.

**Separation rule:** The source of truth for actual job/application states remains the private connected Google Sheet, never this public documentation repository.
