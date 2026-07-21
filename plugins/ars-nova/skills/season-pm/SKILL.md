---
name: season-pm
description: The Ars Nova Project Manager / Dashboard Connector. Lets any team member's Claude (Jonathan, Kimberly, Tom, Zahnay) both PULL from and FEED the master Season Tracker Google Sheet — read what's on their plate / what's due, and add, assign, or complete tasks. Use whenever someone asks what's on their plate, what's due this week, to add/assign/complete a season task, to check a concert or deadline, or to update season planning. Triggers on "season tracker", "dashboard", "my tasks", "what's due", "add a task", "assign", "mark done", "season plan", "26/27 season", "feed the tracker", "pull the tracker". Not for website builds (use ars-nova-web) or proactive inbox sweeps (use project-secretary).
---

# Ars Nova Project Manager / Dashboard Connector

The shared connector every team member loads. It both PULLS (reads the tracker for "what's on my plate / what's due") and FEEDS it (adds, assigns, completes tasks). One master source of truth; never keep a private side-list. It powers the same data the WordPress Season Dashboard shows.

## CONNECTOR FIREWALL — non-negotiable
Use your Ars Nova connectors ONLY, acting as your own @arsnovasingers.org identity (Jonathan = jon@, Kim = kimberly@, Tom = tom@, Zahnay = zahnay@).
- ALLOWED: your Ars Nova Google connector (Sheets/Drive/Gmail/Calendar/Tasks), ars-nova-wordpress-kinsta (DEV), ars-nova-wordpress (LIVE, read-only), ars-nova-facebook, ars-nova-mailchimp, Fathom.
- FORBIDDEN: any personal, other-organization, or default (non-Ars-Nova) Google/Gmail connector. Never act as a non-Ars-Nova account.
If your Ars Nova Google connector is missing, say so — you can't feed or pull the tracker without it.

## The two sources of truth
Locate both by name in your Ars Nova Google Drive (they live together in the 26-27 season folder of the team Shared Drive) — don't rely on a hardcoded ID.
1. Narrative → the season wiki, a Markdown file named "HANDOFF.md". Decisions, context, open questions. Read first for "why".
2. Tabular → the master Season Tracker, a Google Sheet named "Ars Nova 26-27 Season Tracker (Master)". All tasks, concerts, owners, dashboard, per-person views. Also the data source for the WordPress Season Dashboard. If more than one matches, pick the one in the 26-27 season folder and confirm before writing.

Task authority: every task lives in the Season Tracker → Tasks tab. Domain trackers hold non-task data only. Never duplicate task rows. Events belong on the calendar, not as tasks.

## Tracker tabs
- START HERE — conventions + roles legend.
- Tasks — the master list. Columns: Task ID | Bucket | Task | Owner | Status | Priority | Due | Concert/Event | Source | Notes | Created | Updated.
- Concerts & Events — reference (EVT-NN); tasks link via the Concert/Event column.
- Dashboard — live counts.
- Kim / Tom / Jonathan / Zahnay — auto-filtered personal views (read-only QUERY formulas; edit in Tasks).
- This Week — due in the next 7 days.
- Change Log — record every structural edit here.
- Secretary Log — where the Project Secretary sweeps write findings.

## Conventions
- IDs: Task IDs TSK-NNNN, never reused. Read the last ID in column A before adding; increment, zero-pad to 4.
- Status (fixed): Backlog · To Do · In Progress · Blocked · Review · Done.
- Priority: P1 (urgent) · P2 · P3 · P4. Dates: ISO YYYY-MM-DD. Times MT.
- Every task has an Owner and a Status. Stamp Updated on any change.
- Append, don't overwrite: edit the specific cell that changed; never clobber rows in bulk.

## Roles / default owners
- Kim — overall PM / coordination / ops / donor. Owns payroll.
- Jonathan — marketing + web / systems / tech.
- Tom — artistic director / repertoire / programming / auditions.
- Zahnay — personnel manager: roster, attendance→payroll, production logistics, contract-status, audition logistics.
Suggest an owner by zone when a new task has none; don't guess silently.

## Common operations (the loop)
- PULL — "what's on my plate / due this week?" → read the person's view tab or This Week; or QUERY Tasks by Owner and/or Due. Default the person to whoever is running you.
- FEED — "add a task" → append one row to Tasks: fresh TSK ID, Bucket, Owner (suggest by zone), Status "To Do", Priority, Due (ISO/blank), Source, today's Created/Updated. Log a Change Log entry.
- "Mark X done" / status change → set that row's Status + Updated. Never delete rows.
- "Assign to <person>" → set the Owner cell; the person's view + the dashboard update automatically.
- Concerts/deadlines → Concerts & Events + the Dashboard Attention block.
- Promote a PROPOSED row (from a secretary sweep) → fill in owner/fields and drop the "PROPOSED — " prefix.

## Relationship to the other skills
- project-secretary — proactive per-person sweeps that FEED this tracker (email/meeting action items → Secretary Log + tasks). Together they close the loop.
- Website work → defer to ars-nova-web (LIVE vs DEV safety).

## Design constraints
- No emoji in tracker cells. Plain, concise text. Dates ISO.
- Confirm before any bulk/destructive edit. Offer a backup (copy) before restructuring a tab.
