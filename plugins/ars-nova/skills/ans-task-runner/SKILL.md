---
name: ans-task-runner
description: Execute ONE Ars Nova Singers season task end-to-end. Given a task ID (e.g. TSK-0004) or a task description, it pulls the live row from the master Season Tracker, gathers project context + the governing SOP/standard, routes the work to the right skill/tools/owner for the task type, web-searches current best practice, and returns a concrete PROPOSAL (plan + any draft) before doing anything irreversible — then, on approval, does the work and updates the tracker. Trigger on "run TSK-####", "execute/work this task", "ans-task-runner", the "Send to Claude Desktop" brief from the Season Dashboard, or any request to actually complete a specific Ars Nova season-tracker task. NOT for plain tracker queries/updates (use season-pm) or website builds with no task attached (use ars-nova-web).
---

# Ars Nova — Task Runner

Run a single season task the disciplined way: **understand → gather context → check the SOP → route to the right tools → research → propose → (on approval) execute → close the loop in the tracker.** Always stop at a proposal before anything irreversible or externally visible.

## Input
A **Task ID** like `TSK-0004` (preferred), or a task description. If the chat is not already in the **Ars Nova** project, say so and ask the user to switch into it (needed for project docs, SOPs, and the Google connectors). If no task ID is given, ask which task, or infer it and confirm before proceeding.

## Connectors (firewall)
Use your **Ars Nova connectors only**, acting as your own @arsnovasingers.org identity (Jonathan = jon@, Kim = kimberly@, Tom = tom@, Zahnay = zahnay@). Allowed: your Ars Nova Google connector (Sheets/Drive/Gmail/Calendar), `ars-nova-wordpress-kinsta` (DEV), `ars-nova-wordpress` (LIVE, read-only), Fathom, ars-nova-facebook / ars-nova-mailchimp. Forbidden: any personal, other-organization, or default (non-Ars-Nova) connector. If your Ars Nova Google connector is missing, say so — you can't read or update the tracker without it.

## Source of truth
- **Master Season Tracker** — the Google Sheet named "Ars Nova 26-27 Season Tracker (Master)" in the 26-27 season folder of the team Shared Drive (locate by name, as `season-pm` does; ID for reference: `1J0_F58MBUK1vmbpAAnA5EcwshqfiEZGHLr_ARCJhE_M`), tab **Tasks**.
- Column map: `A Task ID · B Bucket(=Tag) · C Task · D Owner · E Status · F Priority · G Due · H Concert/Event · I Source · J Notes · K Created · L Updated · M Done At · N Added By · O Color · P Parent Task ID · Q Links · R Progress % · S Steps`.
- Change Log tab columns: `Timestamp · Actor · Action · Entity · Detail`.
- Per-person tabs (Kim/Tom/Jonathan/Zahnay) read `Tasks!A:L` — never insert columns that would shift them.
- For pure tracker reads / adds / status changes this skill defers to **`season-pm`** (same connector + conventions); `ans-task-runner` is the *executor* that also does the work.

## Workflow

**0 — Resolve the task.** Read the live row for the Task ID (match column A). Echo a one-line header: ID · title · owner · status · due · priority · tag, then the current **Notes** (J) and **Links** (Q). If the ID isn't found, refresh and say so — never guess.

**1 — Gather project context.** Search the Ars Nova project knowledge base (project docs) and the season wiki HANDOFF for anything related. Find sibling tasks (same Bucket/Tag) and any dependencies the notes reference (e.g. "see TSK-00XX"). Follow the task's Links (source emails/files) when they matter.

**2 — Check the governing SOP / standard.** Open the **SOP Library** — the `SOP Library` folder in the **Operations** Shared Drive. This is the org-level, universal "how we do things" store (above any season/project); locate it by name, not a hardcoded ID. Read its `INDEX` and the SOP whose Category/keywords match this task (categories mirror the routing table below), and apply it. For design work also honor the **brand guidelines** (Ligature: periwinkle / pink / navy; medieval-cross + Fibonacci-spiral motifs; live style guides at `arsnovasingers.kinsta.cloud/style-guides/` and `/design-direction/`; the Ruby palette is retired). If a matching skill exists, that skill IS the SOP — load it (see routing). If **no** SOP covers this task type, proceed on best practice and, at close-out, hand the finished task to **`ans-sop-writer`** to capture one for next time.

**3 — Route to the right executor.** Map the task's Bucket/Tag/keywords to the skill + tools + owner, and load them:

| If the task is about… | Route to | Owner usually |
|---|---|---|
| Website / WordPress / Kadence / menu / page / SEO / Kinsta / dev site | **`ars-nova-web`** skill (obey LIVE vs DEV safety) | Jonathan |
| Ticketing / Tickera / WooCommerce / Stripe / checkout | `ars-nova-web` + the Tickera/Woo runbook | Jonathan |
| Branding / logo / ad / print / graphics / design | brand guidelines + image/design tools (`brandkit`, imagegen) + `docx`/`pdf` for print specs | Jonathan |
| Email / outreach / donor letter / announcement | draft in the ANS voice; confirm before any send | Kim / Jonathan |
| Donor development / sponsorship / packages / pricing | `docx` for the doc; pull donor context (Bloomerang) | Kim |
| Repertoire / concert titles / dates / program | gather + reconcile with Tom; web-research pieces/composers | Tom |
| Personnel / roster / auditions / onboarding (Zahnay) | access/setup steps; roster + calendar | Tom / Kim |
| Evaluate / decide / compare / "figure out" | `deep-research` | varies |
| Data / KPI / report / dashboard | the `data:*` skills | Jonathan |

If a task spans types, pick the primary and note the secondary.

**4 — Research best practice.** Web-search the current best way to do THIS specific task (tools, specs, examples, deadlines). Cite 2–3 credible sources. Prefer primary/official sources.

**5 — Propose (STOP here).** Deliver:
- a concrete, numbered plan to complete the task;
- any draft/deliverable you can produce now (copy, outline, spec, checklist);
- open questions + exactly what you need from the owner;
- risks / LIVE-vs-DEV or spend/contract flags.

Then record the plan in the tracker so progress is trackable: write the numbered steps into **Steps** (S) as a checklist — one `[ ] step` per line, all pending — and set **Progress %** (R) = `0`. This is the planned-vs-taken log the dashboard meter reads.

Do **not** take irreversible or externally visible actions (site writes, sends, purchases, contract commitments) until the user approves.

**6 — Execute & close the loop.** On approval, do the work with the routed tools. As you finish each planned step, update progress live (see below). Then update the tracker for this Task ID: set **Status** (E) appropriately, append a dated line to **Notes** (J) — never overwrite — and add any output **Links** (Q). Offer to append a Change Log row (Actor = the real person). Report what changed and what's left. When the task is **Done**, offer to run **`ans-sop-writer`** to capture or refine the SOP for this task type in the Operations SOP Library — especially if step 2 found no governing SOP.

## Progress logging (Steps column S + Progress % column R)
The dashboard shows a horizontal meter + % and the step list from these two columns, so keep them exact:
- **Steps (S):** one step per line. `[ ]` = pending, `[x]` = done. Optionally append ` — <short note>` when the actual step differed from the plan. Add newly-discovered steps as new `[ ]` lines rather than rewriting history. Keep the list to the meaningful milestones (aim for ~4–10 steps), not every micro-action.
- **Progress % (R):** always `round(steps done ÷ total steps × 100)` — recompute and write it every time you check a step off, so the meter and the checklist never disagree. For a task with no discrete steps, you may set an explicit % and leave S empty.
- On full completion: every step `[x]`, **Progress % = 100**, **Status = Done** (which also stamps Done At). If a task is reopened, uncheck the affected steps and let % fall accordingly.
- Resuming later: read S + R first to see what's already done, and continue from the first `[ ]` step.

## Guardrails
- **Ask before irreversible / external actions**: publishing to the LIVE site, sending email, purchases, signing/committing contracts. Offer a backup before changing files (user preference).
- **LIVE vs DEV**: any website change defers to `ars-nova-web` and its `wp_check_environment` safety check; default to DEV unless told otherwise.
- **Never fabricate** facts, amounts, dates, or links. If context is thin, say so and propose how to confirm.
- **Tracker is the source of truth**: merge into Notes, keep updates attributed, don't shift columns A–L.
- Keep the proposal tight and skimmable; the owner should be able to say "go" in one read.

## Related skills / refs
- **SOP Library** — `Operations` Shared Drive > `SOP Library` (the universal standards store; read in step 2).
- `ans-sop-writer` — after a task is done, captures/updates the SOP for that task type in the SOP Library.
- `season-pm` — tracker queries + add/assign/complete (use it for pure tracker ops).
- `ars-nova-web` — all website/WordPress work (LIVE `arsnovasingers.org` / DEV `arsnovasingers.kinsta.cloud`).
- `project-secretary` — proactive per-person inbox/calendar/deadline sweeps that feed the tracker.
- Website Migration Tracker sheet: `1ooD-807jBnxvKJXo7MKou4MlERl36EQE30Qml3tpiKE` (Change Log / handoff for site work).
- Team: Kim Brody (ED), Tom Morgan (Artistic Director), Jonathan Raabe (marketing/tech), Zahnay (Personnel Manager).
