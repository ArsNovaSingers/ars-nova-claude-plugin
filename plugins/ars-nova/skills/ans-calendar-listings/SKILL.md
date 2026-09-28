---
name: ans-calendar-listings
description: List an Ars Nova concert across every community calendar, listing site and music platform in one run — builds the asset pack from live Tickera data, files each web form with Playwright (a headless browser on PULSE) by default, submits on one named "go", and logs what landed where. Covers calendars only, never press contacts.
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
- **One listing = one performance.** Each city's form gets a description naming only that
  performance's date, time and venue (the livestream is mentioned only on the date that is
  streamed). Never put all three dates in one description: calendar editors treat a
  listing as a single event and do not know what to do with a three-city blurb. Write the
  shared programme paragraph once, then end it with the one performance line per outlet.
- One landscape image at **1200x815** and one at **450x300** — two different files.
  Visit Denver recommends 1200x815; Visit Longmont wants 450x300. The Visit Boulder /
  Denver / Longmont forms accept only .jpg/.jpeg/.png **under 750 KB**.

### Which image

Use the concert's **mailer front** (preferred: artwork plus title, guest artist and
dates, so a calendar browser gets the whole message) or the **poster**. Before choosing,
check the artwork against the concert's facts: the Rivers & Streams poster photo shows
Nicolò Spera with a ten-string guitar, which is wrong for that programme, so the mailer
front was used instead. Never use a bare-artwork file when a titled one exists.

To make the listing file from the press PDF (on PULSE, Python + PyMuPDF + Pillow):
render page 1 at 200 dpi, resize to the target width, **trim the 1/8-inch print bleed**
(a 630x414 pt page is 8.75x5.75 in; the trim is width x 9/630 px per side), save JPEG
quality ~86. The press PDFs live in Ars Nova Projects → the season folder → Season
Graphics → Ad Graphics → Mailers.

### Where created artwork goes — always

Every image produced for listings is **copied into the concert's own project folder** in
the Ars Nova Projects shared drive (e.g. `H:\Shared drives\Ars Nova Projects\2026-2027\
1  October 2026 - Rivers and Streams with Nicolò Spera\`), named
`<Concert>-<source>-<WxH>.jpg`. Scratch copies under `C:\Users\jonra\Claud Projects\`
are working files only. Jonathan's standing rule; never leave the only copy in scratch.

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

### form — Playwright on PULSE (DEFAULT)

**Use Playwright for every web form.** Installed 2026-09-28 on PULSE (`python -m pip install
--user playwright` + `python -m playwright install chromium`; v1.63). It is its own clean
Chromium: no password manager, no extensions, no window focus, and no Windows file picker —
`set_input_files()` attaches the image straight from disk. The three What's Happenin'
editions took under two minutes this way after an hour of fighting the Chrome extension.

How to run it: write a small Python script under
`C:\Users\jonra\Claud Projects\Ars Nova\scratch\listing-images\` and run it through
Desktop Commander (`start_process`, `powershell.exe`). The reference script is
`wh_submit.py` in that folder (What's Happenin' ×3). Pattern:

1. **Inspect first, read-only:** open the form headless, dump every input/select/textarea
   with its name, label, type and options, and screenshot it. Map fields from that — never
   guess field names.
2. **One performance per submission,** looping over the events; accept only essential cookies;
   never tick paid "feature this event" upsells.
3. **Run one outlet first** and confirm the success check reads the site's real confirmation
   text; then run the rest.
4. **Stop on the first failure** so nothing is submitted twice.
5. **Save a confirmation screenshot** per submission into the concert's project folder under
   `Calendar listing confirmations\`, and log each one on the Local PM phase ticket.

The Chrome-extension method below is the **fallback only** — for forms behind a login that
exists only in Jonathan's Chrome, or when a human must watch the page.

### form — Claude in Chrome (fallback)

**Approval, done once.** Copy approval comes from Kim (the "listing N of 9" emails). File
the same day she approves — in September 2026 all nine R&S listings were approved on
Sep 24 and then sat unfiled for three days while the concert crept inside two weeks.
Submission approval comes from Jonathan as **one "go" covering a named list of outlets**.
Then work them one at a time, in order, without re-asking between forms:
fill → upload image → submit → confirm the thank-you message is visible → log it.
Stop and ask only if something is off (validation error, wrong city, CAPTCHA).

Take a fresh screenshot immediately before any click near a submit button — a stale
coordinate has published something prematurely before, and on a followed artist page
that reaches real people.

#### Protect the tabs — filled forms are lost on navigate or reload

- **One tab per outlet.** To visit anything else, `tabs_create_mcp` a new tab first.
  Never `navigate` a tab that holds a filled form — that wiped the Visit Boulder form
  once. Never reload.
- **Check state read-only.** Use `find` / `get_page_text` / a read-only script. If a
  tab's ID has changed since you last looked, it was reopened: treat it as empty until
  you have checked its fields.
- **First visit to a site triggers the extension's site-approval prompt.** A
  "Permission denied by user" result means that prompt was declined or timed out. Ask
  Jonathan to click Allow, then retry once. Do not loop.

#### Contact, host and social fields — every form, no exceptions

- **Submitter and every email field: info@arsnovasingers.org.** Not a personal address.
- **Public phone: the office line, 303-499-3165.** Submitter phone may be Jonathan's
  (720-341-6376) where a form asks for one privately.
- **Never enter Kim's name, phone or email on any listing** — not as submitter, not as
  event contact. Standing rule from Jonathan.
- **Host organization:** fill **Other Host Organization: Ars Nova Singers** (Ars Nova is not
  in the Visit sites' member dropdowns). Link the venue from the site's own venue list when it
  is there (VISIT DENVER lists St. Paul at 1600 Grant as "St. Paul Lutheran and Catholic
  Community of Faith").
- **Social fields:** Facebook https://www.facebook.com/arsnovasingers, Instagram
  @arsnovasingers. No Twitter/X account exists; leave it blank. Pinterest: blank until SOC-5
  creates the account. The site footer is the source of truth for official accounts
  (Facebook, Instagram, YouTube channel UCzO5rQsXNYLrBT9gfGOKoEw, Spotify artist
  4XELRDZAJUKCIqYB9zno1w).

#### Dates on the Visit Boulder / Denver / Longmont forms

Point and click. For a single performance choose **One Day** with the start date (the form
itself says so), or, where the form opens on **Custom**, open the custom date box at the
bottom, **click the date in its calendar**, click **Add Date**, and see the date and weekday
appear in the list. Do not type dates into the custom box.

#### Submitting

The real **Submit My Event** button is the one at the very bottom, below the date section.
Some pages carry a second, earlier button reference that does nothing — scroll to the bottom
and click the one you can see. Success is a separate Thank You page; no thank-you means it
did not go.

#### Browser setup

Run listings in a **Claude-only Chrome profile with no password manager or other
extensions.** A password manager draws a frame over sites with email fields (What's
Happenin'), and Chrome then blocks every click and script. Uploads and screenshots only work
on the tab in front of the focused window, so close each finished tab to bring the next
forward.

#### Image — fastest route

Upload the listing image once to the LIVE WordPress media library (over SSH:
`scp` to /tmp, then `wp media import` with the siteurl guard — see
claude/infra/Kinsta_Environments_and_SSH.md). Each form's page script can then fetch it
from arsnovasingers.org and hand it to the form's own uploader input (`multifilectrl` on the
Visit forms); the upload list shows **Complete**. No Windows file picker needed. The
file-picker method below is the fallback.

#### Filling

- **Visit Boulder / VISIT DENVER / Visit Longmont** run the same Simpleview form
  (fields `title`, `startdate`, `starttime`, `location`, `addr1`, `city`, `zip`,
  `admission`, `email`, `linkurl`, `description`, `categories`, `primarycatId`,
  `postname`, `postemail`). Values can be set with a page script.
  **Check the date section on every one of these forms.** VISIT DENVER opens with its
  repeat setting on **Custom** (`recurtype` 99) and an empty custom-date list, which would
  submit an event with no date. Open the Custom panel, click the date box, pick the date
  from the **calendar popup** (typing it gives "Invalid Date"), click **add date**, and
  confirm the date and weekday appear in the list. Verify with the hidden
  `customdates_hiddeninput` field, which must hold the date. Visit Longmont adds
  "Do you have permission to use this image?" — answer **Yes** for Ars Nova's own
  artwork.
- **What's Happenin'** (Boulder / Denver / Longmont editions, one form each) blocks
  page scripts and screenshots — another extension's frame is in the way. Use `find`
  for refs and `form_input` to fill. Check the city dropdown on every edition.
- A script's printed output is blocked if it contains anything that looks like a URL
  query or cookie. Print field names and short values, never the ticket URL.

#### Image upload — the method that works

Click the form's own upload button in Chrome, then type the file path into the Windows
file picker with Desktop Commander:

1. Put the listing image at an **ASCII-only path** (accented folder names like "Nicolò"
   do not survive typed keystrokes), e.g.
   `C:\Users\jonra\Claud Projects\Ars Nova\scratch\listing-images\<file>.jpg`.
2. Start this PowerShell helper via Desktop Commander (`start_process`, shell
   `powershell.exe`, short timeout so it keeps running in the background):

   ```powershell
   $ws = New-Object -ComObject WScript.Shell
   $path = 'C:\Users\jonra\Claud Projects\Ars Nova\scratch\listing-images\<file>.jpg'
   $ok = $false
   for ($i = 0; $i -lt 20; $i++) { Start-Sleep -Milliseconds 500; if ($ws.AppActivate('Open')) { $ok = $true; break } }
   if (-not $ok) { 'NO_DIALOG'; exit }
   Start-Sleep -Milliseconds 600; $ws.SendKeys('%n')
   Start-Sleep -Milliseconds 300; $ws.SendKeys($path)
   Start-Sleep -Milliseconds 400; $ws.SendKeys('{ENTER}'); 'SENT'
   ```

3. Within 10 seconds, click the form's upload button (`computer` → `left_click` on the
   button's ref). On the Simpleview forms that is the visible **"upload images"**
   button — not the hidden `mediafile` input, which does not count.
4. `read_process_output` should say `SENT`. Then confirm the file shows in the form's
   upload list with status **Complete**.

What does NOT work, so nobody re-tries it: the Chrome `file_upload` tool (rejects local
paths); fetching the image from a local web server inside the page (Chrome's Local
Network Access blocks it pending a user prompt); setting the hidden `mediafile` input
by script. A script *can* fetch images from arsnovasingers.org (its uploads allow
cross-origin reads), but only files already in the media library.

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

## Outlet status notes — verified September 2026

- **CPR Classical:** the old submit-event address now redirects to a directory page with
  no submission form. Find the current route or mark `N/A — no public form`.
- **Denver Life Magazine:** unreachable through the Chrome extension on 2026-09-28 (site prompt
  declined); retry it with Playwright.
- **What's Happenin':** free, human-reviewed listings; approval notice goes to the submitter
  email. Paid "Featured Event" upsell ($149 / $249 / $449) — leave off unless the budget holder
  says yes.

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
