---
name: ans-sop-writer
description: Capture how a piece of Ars Nova work was actually done into a reusable SOP, categorize it, and file it in the org-level SOP Library (Operations Shared Drive) so it guides all future work of that type. Given a completed task (e.g. TSK-0004) or a just-finished piece of work, it reviews the steps taken, checks whether an SOP already exists (updates it rather than duplicating), distills the repeatable procedure from the TEMPLATE, files it in the matching category folder as a DRAFT, and registers it in the INDEX. Trigger on "write an SOP", "capture this as an SOP", "document how we did this", "ans-sop-writer", or the close-out handoff from ans-task-runner. NOT for writing season work product (that lives in the tracker/project), and NOT for tracker ops (use season-pm).
---

# Ars Nova — SOP Writer

Turn finished work into a durable standard. SOPs are **universal** — "how we do things," org/ops level, above any season or project. They live in one place and govern all future work of that type, in or out of a project. This skill captures them; it never writes season-specific work product.

## Connectors (firewall)
Use your **Ars Nova connectors only**, acting as your own @arsnovasingers.org identity. Primary: your Ars Nova Google connector (Drive/Docs/Sheets). Forbidden: any personal, other-organization, or default (non-Ars-Nova) connector. If your Ars Nova Google connector is missing, say so — you can't read or write the SOP Library.

## Where SOPs live
- The **SOP Library** = the `SOP Library` folder in the **Operations** Shared Drive. Locate it by name (not a hardcoded ID). It sits at the org/ops tier — a sibling of the season "Projects," never inside a season folder or a Claude Project.
- Structure: one folder per **category** (Website & Systems · Ticketing & Commerce · Branding & Design · Donor Development & Sponsorship · Repertoire & Concerts · Personnel & Auditions · Data & Reporting · General & Cross-Cutting — these mirror the `ans-task-runner` routing table). One SOP per Google Doc, built from **`TEMPLATE — SOP`**. A registry lives in **`INDEX — SOP Library`**.

## Workflow

**0 — Gather what was done.** Take the Task ID (or the described work). Read the tracker row — the **Notes** (J), the **Steps** log (S = the planned-vs-taken checklist), and **Links** (Q) — plus what actually happened in this session. Identify the task **type** and the matching **category**.

**1 — Check for an existing SOP (don't duplicate).** Open the SOP Library, read the `INDEX`, and look in the matching category folder for an SOP whose keywords cover this task type. If one exists → **UPDATE** it: fold in what was learned, refine steps, bump *Last updated*, add this Task ID to *Source task IDs*. Only create a new SOP when none covers it.

**2 — Distill the procedure.** Using `TEMPLATE — SOP`, write the **generalizable** how-to: the repeatable steps, tools/skills, standards/guardrails, common pitfalls, and definition of done. Strip one-off specifics (particular dates, names, values) unless they're universally true. Fill the front matter: Title (verb-first), Category, Applies-to keywords (what `ans-task-runner` will match on), **Status: Draft**, Owner (the role accountable), Last updated (today), Source task IDs.

**3 — File it.** Create (or update) the Google Doc in the matching **category folder** under `Operations > SOP Library` (locate the folder by name). Keep one SOP per doc.

**4 — Register it.** Update the `INDEX — SOP Library` registry line: `SOP — Category — Status — Last updated`.

**5 — Report + hand off for review.** SOPs ship as **Draft**; a human promotes to **Reviewed** — never self-canonize. Report the SOP name, category, link, and whether it was new or an update, and note it's awaiting review.

## Guardrails
- **Draft, not gospel.** New/updated SOPs are Draft until a person marks them Reviewed.
- **Update, don't duplicate.** Always search the category first; fold learnings into the existing SOP when there is one.
- **Universal, not work product.** Capture the repeatable method, not the season-specific output. Season work product stays in the Season Tracker / season Project.
- **Concise + generalizable.** Meaningful steps only. Plain text, dates ISO.
- **Never fabricate.** Base the SOP on what was actually done; if a step is uncertain, mark it as "verify".

## Related skills / refs
- `ans-task-runner` — executes tasks and, at close-out, hands finished work here to capture the SOP.
- `TEMPLATE — SOP` / `INDEX — SOP Library` — in `Operations > SOP Library`.
- `season-pm` — tracker ops (source of the task row you read in step 0).
- Team: Kim Brody (ED), Tom Morgan (Artistic Director), Jonathan Raabe (marketing/tech), Zahnay (Personnel Manager).
