---
name: ans-calendar-listings
description: List an Ars Nova concert across every community calendar, listing site and music platform in one run — builds the asset pack from live Tickera data, works the outlets in priority order with a confirm before each submit, and logs what landed where. Covers calendars only, never press contacts.
---

# Ars Nova — Concert Calendar Listings

One concert in, the whole outlet list out. Builds the asset pack, works every calendar,
directory and music platform in priority order, and records what landed where.

Trigger on "list <concert> on the calendars", "submit the calendar listings", "get
<concert> out to the calendars", "run the listings", "ans-calendar-listings", or the
calendar-listings phase ticket on a concert's Local PM marketing board.

NOT for press pitches — see "The two lists do different jobs" immediately below.

## The two lists do different jobs

The submission tracker has a Calendars tab and a Press Contacts tab. They are not the
same work and must never be run the same way.

- **Calendars** — self-serve listings. No release, no relationship, mostly a form.
  This is volume work: every outlet, every concert, every time. **This skill covers these.**
- **Press Contacts** — human beings who decide what gets written about. A handful of
  people, pitched individually, each with a real angle. **This skill never touches them.**

Kim's rule, and she is right: treating the second list like the first is what makes press
outreach stop working. If asked to "do the press too" in the same pass, say so and stop.

## Connector firewall

Ars Nova connectors only, acting as your own @arsnovasingers.org identity. Never
`aromis-*`, `personal-google` or the default `gmail`. Concert facts come from
`ans-wordpress-live` (Tickera + WooCommerce); Drive and Sheets via the Ars Nova Google
connector; Facebook Events via `ars-nova-facebook`; web forms via Claude in Chrome.

## Source of truth

- **Outlet master and field specs:** the Google Sheet **"Concert Listing Calendars —
  Boulder / Denver / Longmont (Submission Tracker)"**, in Ars Nova Projects →
  **Marketing Playbook**. Locate it by name rather than a stored id — it has moved once
  already.
  - `Calendars` tab — one row per outlet. Columns A–N are Kim's research; **O–S are the
    machine-readable spec**: Submit method, Which dates to file, Image spec, Category to
    select, Account status.
  - `Strategy` tab — the doctrine and the verified corrections. Read it before a first run.
  - `Submission Log` tab — per-concert history. You append here.
- **Concert facts:** Tickera and WooCommerce, queried live. Never a document.

## Step 1 — get the concert right, from live data

Never take dates, times, venues or prices from a HANDOFF, a previous paste-pack, or
memory. Query them every run:

- `tickera_list_events` / `tickera_get_event` — performances, dates, venues
- `wc_list_products` — real ticket prices, including student/youth and livestream

Record per performance: date, start time, venue name, full street address, city, ZIP.
Then read them back. A wrong city is the most expensive mistake in this work.

## Step 2 — build the asset pack

The Strategy tab specifies exactly this, and none of it is optional:

- Concert title, plus a short (~40 character) variant for tight fields
- Date, start time, venue name and full address — per performance
- Ticket price and ticket URL
- A **50-word** description and a **150-word** description
- One landscape image at **1200x815** and one at **450x300** — two different files.
  Visit Denver hard-validates 1200x815; Visit Longmont wants 450x300.

Append `?utm_source=<outlet>&utm_medium=listing&utm_campaign=<concert-slug>` to the
ticket URL wherever a query string survives, so the channel is measurable afterwards.

## Step 3 — the city-matching rule

Most outlets are city-scoped. A listing filed against the wrong city is rejected, and
that is the single biggest source of wasted effort here. Column P carries the rule per
outlet. Before every submit, re-read the address you are about to enter and confirm the
city matches the outlet.

Two traps worth naming:

- **What's Happenin' is one form with three city editions.** The dropdown may remember
  the previous choice. Re-check it on every submission. If two confirmations name the
  same city, one went to the wrong edition and needs redoing.
- **Check whether one performance starts at a different time.** For Rivers & Streams the
  Longmont matinee is 4:00 PM while the others are 7:30 PM, and it is easy to autopilot
  the wrong time in. Verify against Tickera rather than assuming a pattern.

## Step 4 — venue-dependent eligibility, re-checked every concert

Some outlets are excluded by the current venue, not forever. Re-evaluate each time:

- **Downtown Boulder Partnership** — downtown district only; eligible only if the Boulder
  venue sits in the Pearl Street district.
- **Downtown Longmont / Creative District** — Main Street corridor only.
- **CU Boulder calendar** — a CU venue or a CU co-presentation only.
- **Boulder Arts Week** — the April window only; registration opens Jan–Feb.
- **Chambers of Commerce** — a membership gate, not a signup. Confirm current membership
  or mark the row N/A.

Do not carry a previous concert's SKIP forward without re-checking it against this
concert's venues.

## Step 5 — work the outlets by submit method

Column O tags each outlet. Run in this order.

### dashboard / api — highest leverage, do first

- **Bandsintown** (artist `904368`, account live) — the three dated events on the artist
  profile. It syndicates to Spotify, Apple Music and Google automatically, so it is worth
  more than any single local calendar. Dates must be typed `YYYY-MM-DD`; the US format is
  rejected with a misleading "within two years" error. Venue autocomplete's *verified*
  entry can be the wrong state — read the city on every suggestion. Full trap list:
  `claude/marketing/Bandsintown_Artist_Page_Setup_2026-09-21.md`.
- **Facebook Events** — through the `ars-nova-facebook` connector. Create as the PAGE,
  not a personal profile. A livestreamed date stays IN PERSON with the livestream
  mentioned in the text; do not flip the event to online-only.
- **Eventbrite** — listing only. **Never sell tickets here.** Ars Nova sells through
  Tickera; set external ticketing pointing back at the site. Getting this wrong creates a
  second sales channel with its own fees and its own attendee list.
- **Songkick** — after Bandsintown, which it overlaps. First to drop if time is short.
- **Colorado.com** — the partner account needs human approval; allow 2+ weeks. Start it
  early or accept that it misses the date.

### form — Claude in Chrome, with a confirm before each submit

Fill the form from the asset pack, then **show the filled form and wait for a yes before
submitting**. One outlet at a time, never batch-submitted.

Load the browser tools in a single ToolSearch call. Take a fresh screenshot immediately
before any click near a submit button — a stale coordinate has published something
prematurely before, and on a followed artist page that reaches real people.

### email — draft, never send

Draft each submission and hand it over for approval. External sends are the user's call,
every time. Where Claude wrote the text, the email says so.

Three of the email outlets are Priority 1 — Do303, Westword, and Boulder County Arts
Alliance. The email pass is not optional cleanup; it carries as much weight as the forms.

### website change — not a submission at all

**Google Event structured data** is schema.org markup on the concert pages, not a form to
fill in. It is the highest-ROI item on the whole list and it benefits every future
concert. Route it to the website branch; never try to "submit" it.

## Step 6 — log what happened

For every outlet touched, append a row to the `Submission Log` tab: concert, performance
date filed, outlet, status, date actioned, submitted by, live listing URL, confirmation
reference, notes. Status values: `SUBMITTED`, `LIVE`, `REJECTED — <reason>`,
`N/A — <reason>`.

Also set column L on the `Calendars` tab to the latest state, so one glance at the master
shows where things stand.

Mark ineligible outlets `N/A — <reason>` rather than leaving them "Not started", or the
next person re-litigates a decision that was already made.

## Lead times — what has already closed

- Weeklies and features: 3–4 weeks
- Monthly print: 6 weeks
- Seasonal guides (Westword Fall / Winter / Summer arts): their own, much earlier deadlines
- Weekly newsletters (What's Happenin', The Mountain-Ear): 2+ weeks out
- Visit Denver: a published two-week review window — the only outlet that states one

If the concert is inside three weeks, say plainly which outlets are already past their
window instead of submitting into a closed door.

## Verified corrections — do not undo these

From the Strategy tab, verified September 2026:

- **Peter Alexander has retired.** He wrote Boulder Weekly's classical coverage as well,
  so Boulder currently has no dedicated classical critic. Do not pitch him.
- **CPR Performance Studio is defunct.** CPR's own Colorado Spotlight page still
  advertises studio sessions; that copy is stale. Colorado Spotlight itself IS still on
  air — pitch airplay, not a studio session.
- **Outlet websites carry stale copy.** Verify with a person before building a plan on it.

Email patterns, confirmed and useful:

- Prairie Mountain Media (Daily Camera, Longmont Times-Call, Broomfield Enterprise,
  Colorado Hometown Weekly): `firstinitial` + `lastname` @prairiemountainmedia.com
- CPR: first initial + lastname, spaces and punctuation stripped, @cprmail.org

Confirm a guessed address before using it. A bounce to an editor is a bad first impression.

## What this skill must not do

- Touch the Press Contacts tab.
- Sell tickets anywhere but Tickera.
- Send an external email without explicit approval.
- Buy a paid upgrade. Several outlets have one; free tier only, unless whoever holds the
  budget has said yes.
- Put an instrument detail in public copy unless Tom has confirmed it for that concert.
  A "ten-string guitarist" line reached radio copy, three blog posts and a press release
  before anyone caught that the programme uses a six-string.
- Post to Nextdoor from a personal profile — nonprofit page or not at all.
- Frame the concert as a fundraiser or benefit in any description. Visit Boulder and
  Visit Denver both exclude fundraisers, and that wording hands them a reason to decline.
