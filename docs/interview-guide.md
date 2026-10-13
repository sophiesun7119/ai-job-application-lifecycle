# Interview guide: 90-second narrative and engineering questions

## 90-second story

"I built an AI-assisted job application workflow because repeating company discovery, ATS checks, resume edits and recruiter email triage was costly and error-prone. I started with a deterministic Python/SQLite collector for seven ATS adapters, then used separate scheduled AI workflows and Google Sheets as the state store. Company discovery runs daily, job monitoring runs hourly in 25-company Tier batches, and a separate Gmail tracker reconciles incoming mail against applications that I actually submitted.

The main engineering challenges were identity and safe state updates: avoid re-adding a requisition when its URL changes, preserve human Skip/Applied decisions, distinguish blocked sites from unattempted companies, checkpoint progress across constrained tasks, and prevent late confirmation emails from regressing an interview status. I also built an evidence-based resume tailoring skill using real resume and Story Bank content; I approve meaningful edits and always submit applications myself.

I use deterministic processing for repeatable identity/state logic and AI for open-ended research and JD interpretation. I record partial work and failures instead of claiming perfect automation. Three public repos show my collector, scheduler prompt tool and tailoring skill; this case study documents their relationships and the separately configured runtime."

## Technical follow-ups

**Why Google Sheets rather than PostgreSQL?** Low operational overhead and human inspectability for a personal workflow; transactionality and concurrency are weak, so use stable IDs, narrow writer responsibilities, readback and reconciliation. This choice may change as scale grows.

**How do you prevent duplicate jobs?** Stable ATS provider/company/requisition ID first; normalized role-specific official URL second; all historical rows (including closed/skipped/applied) participate; re-read before append; preserve First Seen. Generic careers homepage URLs are not unique.

**What happens when an ATS website fails?** Classify attempted-but-incomplete as BLOCKED with observed reason, not CHECKED and not CLOSED; persist Scan Status and partial Run receipt, then retry fairly. Never invent errors for sites not attempted.

**Why 25-company batches?** A constrained schedule cannot guarantee a full large roster per invocation. Bound work and save Tier/batch checkpoint to improve transparency and make coverage measurable over cycles rather than hourly fantasy.

**Are the Python collector and Google Sheet tasks the same service?** No. The local scanner is a script-first precursor; the scheduled AI workflow uses separate connected-host tools and prompts. The prompt generator authors runnable task text but does not itself schedule or execute it.

**How are email statuses ordered?** ATS ID and direct role link are strongest identifiers, followed by company+title/thread/date. Match to one existing application only. Avoid regressing OA/Interview with a delayed confirmation; send ambiguous same-company cases to review.

**How do you verify resumes?** Match JD requirements to actual source evidence, compare base resume options, ask approval for material edits, copy rather than overwrite the native Google Doc, export and render PDF to count pages. If verification unavailable, say so.

**What are the biggest limitations?** Cross-service writes and chat delivery are not atomic; prompt notifications differ from platform push settings; dynamic ATS often blocks scans; daily Company Discovery uses legacy status semantics that must be separately reconciled.

## Evidence and scope discipline

- 145 companies and 512 jobs are **dated 2026-10-12 Sheet snapshot counts**, not currently open roles.
- 78 seeded companies, 75 completed boards and 14,178 deduplicated identities refer to **the separate local scanner**.
- ~300 emails over ~3 weeks is a **user estimate**; no measured hours saved or classification precision.
- This is not a production Kubernetes service, custom LLM API product, fully integrated OAuth app or automatically submitted job application system.
