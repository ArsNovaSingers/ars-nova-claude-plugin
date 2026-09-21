---
name: ans-marketing-project
description: Stand up or audit the full marketing campaign board for one Ars Nova concert in Local PM, from the Marketing Action Items Template, with due dates computed backwards from the first performance.
---

# Ars Nova — Marketing Project Builder

Turns one concert into a working Local PM board: ten phase tickets, the SOP checklist as
subtasks, owners, and due dates computed backwards from the first performance. Also runs the
weekly what's-due sweep and the mid-campaign audit against the same template.

Trigger on "set up marketing for <concert>", "build the marketing board", "what's due on
<concert>", "audit <concert> against the SOP", "ans-marketing-project".

NOT for writing the actual copy, artwork or posts — that is the work the tickets describe.
NOT for the Season Tracker (`season-pm` owns that, and per `PROJECT_RULES.md` §1 marketing
execution does not go there at all).

## Connector firewall
Ars Nova connectors only, as your own @arsnovasingers.org identity. Never `aromis-*`,
`personal-google` or the default `gmail`. Local PM is `Local-PM-Ars`; concert dates come from
`ans-wordpress-live`.

## The source of truth
`claude/marketing/Marketing_Action_Items_Template_2026-09-15.md` (mirror) and the Google Doc
**Marketing Action Items Template** in Ars Nova Projects → **Marketing Playbook** (it lived in the
drive root until 2026-09-21). Locate it by name rather than by a stored id. Read it before building — it changes as the team learns, and
the phases below are its headings, not a second list to maintain.

Outlets, named editors, eligibility and lead times live in the **Concert Listing Calendars
Submission Tracker**, in the same Marketing Playbook folder — Calendars, Press Contacts, Strategy
and Submission Log tabs. To actually work the calendar listings, hand off to
**`ans-calendar-listings`**; this skill builds the board, that one runs the submissions.

## Mode 1 — Build a board

**1. Get the real dates from Tickera, not from a doc.** `tickera_list_events` on
`ans-wordpress-live`. Collect every performance of the concert, its venue, and any livestream
event. A concert may have two to four nights across three cities.

**2. Anchor on the FIRST performance.** Every due date is computed backwards from it — not the
Denver date, not the last night, not a date someone says in chat. State the anchor out loud.

| Phase | Offset | Due |
|---|---|---|
| 1 Asset pack | T-12 to T-10 weeks | T-10w |
| 2 Website and ticketing | T-10 weeks | T-10w |
| 3 Long-lead print and radio | T-8 weeks | T-8w |
| 4 Press pitches | T-6 to T-4 weeks | T-5w |
| 5 Calendar listings | T-4 to T-3 weeks | T-3w |
| 6 Direct mail | T-4 to T-2 weeks | T-4w (file to printer) |
| 7 Email and social | T-3 weeks to concert week | T-1w |
| 8 Comps | T-2 weeks | T-2w |
| 9 Concert week / day of | 2 days before the first night | |
| 10 Post-concert follow-up | last night + 7 days | |

**3. Check for an existing project first.** `list_projects`. If one exists for this concert,
switch to Mode 3 and audit it — never create a second board for the same concert.

**4. Create the project.** Name `<Concert> — Marketing`, short uppercase prefix. The description
carries: every performance with date, time and venue, verified from Tickera today; the anchor
date and the computed T-minus table; a pointer to the template; and any standing decision that
affects this concert (a material not yet signed off, a videographer deliberately not booked, a
venue constraint).

⚠️ **`description` takes RAW HTML.** Pass real `<p>` and `<strong>` tags. HTML-escaping them
stores literal `&lt;p&gt;` markup — that happened on the first attempt, 2026-09-16.
⚠️ **Do not pass `color`** on create — an arbitrary hex is rejected as "invalid selection".
Leave it default and set it afterwards with `update_project` if it matters.

**5. Create one ticket per phase**, skipping any phase that genuinely does not apply (a concert
with no mailer has no Phase 6) and saying why in the report. Each ticket gets:

- the phase's checklist items as **subtasks** — this is the mechanism; plain text, no markdown
- a due date from the table
- an owner via `team` (Jon `6a71f000acfa2b00532df4a5`, Kimberly `6a71f002acfa2b00532df4a9`,
  Tom `6a71f001acfa2b00532df4a7`, Zahnay `6a71f003acfa2b00532df4ab`)
- a description carrying the *reason* the phase matters and the traps specific to it — not a
  restatement of the subtasks

⚠️ **Assign an owner to every ticket.** As of 2026-09-16, 37% of all Local PM tickets have no
owner and six of twelve projects have none on any ticket. An unowned ticket is a note, not a
task, and it is the single thing most likely to make this board fail the way a shared list does.

**6. Localise, don't copy.** A generic board is ignored. Put this concert's facts in: which city
takes which listing, which angle the press pitches lead with, which night is livestreamed, which
sign-off is outstanding. Phase 5 names Boulder, Denver and Longmont outlets — assign each to the
performance in that city, because a listing filed against the wrong city is rejected.

**7. Report** the project prefix, every ticket ID and title, the anchor date, and any phase
skipped with its reason.

## Mode 2 — What's due

`list_tickets` filtered to the concert's project, `include` dueDate, priority, team, subtasks.
Report what is overdue, what is due this week, and what is blocked — with subtask-level detail,
because a phase ticket is usually half done rather than untouched.

Lead times are what make something urgent, so state them: monthly print needs 6+ weeks, weeklies
and features 3–4 weeks, CPR host notes 2–3 weeks, online calendars 2+ weeks.

⚠️ **A phase whose lead time has passed is not "late", it is gone.** Say so plainly, move it to
Phase 10 as a finding, and do not leave a ticket nobody can action. Rivers & Streams lost its
whole monthly-print channel this way and nothing surfaced it until an audit went looking.

## Mode 3 — Audit an existing concert

Read the board, read the template, report only the gaps — phases with no ticket, tickets with no
owner, subtasks that should have been ticked by now given today's date. Then offer to create the
missing ones as a catch-up pass.

⚠️ **Do not duplicate work that already exists under another name.** `RSM` had nineteen tickets
covering blog and social content before it had any SOP phases; the catch-up added only what was
missing. Read the existing titles first.

## Guardrails

- **Never create a second project for a concert that already has one.**
- **Never open Season Tracker rows for individual phases.** `PROJECT_RULES.md` §1, rewritten
  2026-09-16: execution records in Local PM, the Tracker keeps the season-level view, and
  dual-writing is what let the two disagree for two days in August.
- **Verify dates from Tickera in the session you build the board.** A date from a HANDOFF is a
  historical claim, and the Tracker's Concerts & Events tab has been measured wrong against live
  Tickera data.
- **The calendars are volume work; the press list is relationship work.** Never generate a
  subtask that implies blasting the press contacts.
- **Unknowns get a `CONFIRM:` subtask with a named owner** — never a blank, never an omission.
- No emoji in ticket titles. Dates ISO.

## Related
- `claude/marketing/Marketing_Action_Items_Template_2026-09-15.md` — the phases and checklists
- `claude/marketing/HANDOFF.md` §1g — how this came about and what is still unconfirmed
- `claude/marketing/Concert_Reminder_Emails_SOP.md` — the Phase 7 ticket-holder mechanism
- `claude/marketing/Mailer_Attribution_System_2026-09-09.md` — the Phase 6 coupon and QR traps
- `season-pm` — the Season Tracker, which this skill deliberately does not write to
- `ans-signoff` — run at the end, as always
