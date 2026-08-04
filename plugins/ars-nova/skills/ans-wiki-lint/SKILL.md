---
name: ans-wiki-lint
description: Periodic health-check for the Ars Nova project's own documentation (claude/*.md in this Claude Project) — finds contradictions between docs covering the same ground, docs nobody links to anymore (orphans), and docs that look stale against a still-active initiative. Inspired by Karpathy's "LLM Wiki" pattern (raw sources / wiki / schema + Ingest / Query / Lint). Never deletes or silently rewrites — proposes a dated Lint Report and, for clear-cut cases, offers to update Initiative_Index.md's Status/Superseded-By columns with a backup offered first. Trigger on "lint the docs", "check for stale specs", "doc audit", "wiki lint", "ans-wiki-lint", or as a periodic step inside a project-secretary sweep or an ans-sop-writer close-out. NOT for linting code or plugin files (no equivalent skill needed there — the Plugin Build Registry already serves that function for code), and NOT for tracker hygiene (Season Tracker dedup is season-pm's "search before you append" rule).
---

# Ars Nova — Wiki Lint

Keep the project's own documentation honest. This project's `claude/*.md` files are a wiki in
Karpathy's sense: an LLM-maintained synthesis layer over raw sources (the Season Tracker, Gmail,
Fathom, git history), not a source of truth in themselves. Wikis drift — two docs end up covering
the same initiative, one goes stale while its sibling gets updated, a doc nobody links to anymore
sits there implying it's still current. Nothing else in this system checks for that. This skill
does, on a recurring basis, not as a one-time cleanup.

## Connector firewall
Use your **Ars Nova connectors only**, acting as your own @arsnovasingers.org identity. This skill
mainly reads/writes **project docs** (via the Projects tool, not a connector) and, secondarily, may
cross-reference the Season Tracker (Ars Nova Google connector) to check whether a doc referenced
from a task's Links/Notes still exists and still matches. Forbidden: any personal, other-organization,
or default (non-Ars-Nova) connector.

## Baseline (read these first, every run)
- `claude/Initiative_Index.md` — the canonical map of initiative → spec doc → status (if this
  doesn't exist yet, say so and offer to build it before linting — Lint needs an index to check
  docs against).
- `claude/WIKI.md` and the relevant branch `HANDOFF.md`(s) — current state; anything a branch
  HANDOFF references as active should show up correctly in the Index.
- `claude/PROJECT_RULES.md` — the schema layer; check it isn't itself contradicted by anything in
  a branch HANDOFF or a spec doc.

## What counts as a finding
1. **Duplicate-canonical** — two or more docs both appear to be the governing spec for the same
   initiative (e.g. two docs both describing the Season Dashboard's data model with different
   details). Flag both; do not guess which one is "right."
2. **Contradiction** — the same fact (a version number, a date, a column map, a price, a status)
   stated differently in two docs that are both still referenced as current. Quote both statements
   verbatim in the finding so a human can resolve it without re-reading either doc in full.
3. **Orphan** — a doc not referenced by `Initiative_Index.md`, `claude/WIKI.md`, any branch
   HANDOFF, or any other live doc, and not clearly a dated point-in-time record (audits, findings
   docs, and backups are expected orphans — don't flag those as a problem, just note them as
   archival).
4. **Stale** — a doc tied to an initiative `Initiative_Index.md` marks "In Progress" or "Approved,"
   but the doc itself hasn't been touched in a long stretch relative to how active that initiative
   actually is (cross-check against the Season Tracker: are there recent Task updates on this
   initiative with no matching doc update?).

## Workflow

**1 — Baseline.** Read the three baseline docs above. If `Initiative_Index.md` is missing or
clearly out of date (doesn't list an initiative that a branch HANDOFF or the Tracker shows as
active), say so up front — findings below it will be less reliable until it's current.

**2 — Sweep.** Read (or `project_search` where the doc set is large) every `claude/*.md` doc that
isn't itself a dated backup, output artifact, or point-in-time audit — this now spans the branch
folders (`website/`, `ticketing/`, `marketing/`, `plugins/`, `portal/`, `season-ops/`, `infra/`)
as well as root docs; folders are a naming convention only, so sweep every branch, not just root.
Group by apparent initiative (match against `Initiative_Index.md` rows). For each group of 2+
docs, diff them for contradictions and duplicate-canonical claims. For docs matching no group,
evaluate orphan/stale.

**3 — Do not act unilaterally.** This skill never deletes a doc, never silently merges two docs,
and never rewrites a spec's content. The one exception: when a doc is **unambiguously** superseded
(a newer doc says so explicitly, or a branch HANDOFF names the newer one as current), it may update
`Initiative_Index.md`'s `Status` / `Superseded By` columns to reflect that — offer a backup of
`Initiative_Index.md` first, per standing preference, and say plainly what changed.

**4 — Report.** Write a dated Lint Report (`claude/Lint_Report_<YYYY-MM-DD>.md`) listing every
finding by type (Duplicate-canonical / Contradiction / Orphan / Stale), each with: the doc(s)
involved, a one-line description, and a suggested next step (e.g. "confirm X is current, archive
Y" — never "I archived Y"). Keep it skimmable — a table, not prose, when there are more than a
handful of findings.

**5 — Hand off, don't resolve.** End with a one-paragraph summary: how many docs were checked, how
many findings by type, and the single most important one to resolve first. If a finding implicates
a task-relevant fact (e.g. a contradicted version number that affects an in-progress build), flag
that explicitly and suggest the user or the relevant skill (`ans-task-runner`, `season-pm`) confirm
it before it causes a real mistake — this skill surfaces the problem, it does not adjudicate which
doc is right.

## Guardrails
- **Never delete a doc.** Orphan and stale findings are proposals to review, not to remove.
- **Never rewrite spec content to resolve a contradiction.** Only a human (or the skill that owns
  that spec) resolves which version is correct.
- **The one write this skill makes unprompted** is a Status/Superseded-By update to
  `Initiative_Index.md` for unambiguous cases — always with a backup offered first.
- **Don't flag expected orphans** — dated audits, backups, and point-in-time findings docs (like
  this one) are supposed to sit outside the live index. Flagging every dated doc as an "orphan"
  would make the report useless noise.
- No emoji. Dates ISO. Keep findings terse enough that a human can act on the report without
  re-opening every doc it names.

## Related skills / refs
- `ans-sop-writer` — captures repeatable *work* into SOPs; this skill keeps the *documentation
  about* that work honest. Natural to run Lint right after an SOP capture, since that's when a doc
  is most likely to have just gone stale relative to a sibling.
- `season-pm` — "search before you append" is the Tracker's version of this same discipline,
  applied to task rows instead of docs. This skill is the doc-layer equivalent.
- `Plugin_Build_Registry_and_Handoff.md` — the pattern this skill generalizes: a registry doc that
  is the map, kept honest against what's actually true. That doc already does this well for the 9
  plugin repos; `Initiative_Index.md` + this skill do the same job for specs.
- `claude/Initiative_Index.md` — the baseline this skill reads and, in narrow cases, updates.
- `claude/WIKI.md` — the branch index this skill sweeps across; new branches added there
  automatically fall into scope on the next run.
