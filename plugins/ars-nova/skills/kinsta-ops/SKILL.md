---
name: kinsta-ops
description: >-
  Operate the Ars Nova site at the HOSTING layer through the `ars-nova-kinsta`
  MCP connector (Kinsta API) - list sites and environments, clone an
  environment, push one environment over another, clear the Kinsta full-page
  cache, and poll async operations. Trigger on "create a staging site", "clone
  the environment", "push staging to live", "clear the Kinsta cache", "what
  environments exist", "is the staging site up", "kinsta-ops", or any request
  that needs the hosting platform rather than WordPress itself. NOT for editing
  pages, posts, plugins or theme settings - that is the `ars-nova-wordpress-*`
  connectors (use ars-nova-web). NOT for the DNS cutover
  (`ANS_Launch_Infrastructure_Plan.md`).
---

# kinsta-ops

## 0. Which connector am I in

Three connectors have confusingly similar names. Get this right before doing
anything:

| Connector | Talks to | Use it for |
|---|---|---|
| `ars-nova-wordpress` | WordPress on `arsnovasingers.org` | pages, posts, plugins, users |
| `ars-nova-wordpress-kinsta` | WordPress on `arsnovasingers.kinsta.cloud` | same, on DEV |
| **`ars-nova-kinsta`** | **the Kinsta hosting API** | **environments, clone, push, cache** |

The last one is this skill. The middle one has "kinsta" in its name and is NOT
the hosting connector - it is WordPress. That collision is the single most
likely way to act on the wrong thing.

## 1. Known state (verified 2026-08-08, first live call)

- Company ID `6670d1b8-dd26-4df3-b230-29b546de2796`
- One site: **Ars Nova Singers**, `75a768cf-8b61-441e-b6a8-79f0e2133582`
- One environment: **Live**, `a2b54153-7f76-419d-9ca1-35372bd52224`, `is_premium: true`,
  PHP 8.5, web root `/www/arsnovasingers_429/public`
- **No staging environment exists.** Phase 0 of
  `claude/website/ANS_Post_Launch_Dev_Environment_Plan.md` has not been run.
- The Live environment already carries `arsnovasingers.org` and `*.arsnovasingers.org`
  as attached domains, though `primaryDomain` is still `arsnovasingers.kinsta.cloud`.
  Hosting-level attachment is done; DNS is not.

Re-read rather than trust: ids are stable, but environment lists change the moment
someone clones. Call `kinsta_list_environments` first, every time.

## 2. The workflow

### Creating a staging environment

1. `kinsta_list_environments` - confirm what exists. Do not clone blind.
2. `kinsta_clone_environment` with the LIVE env as `source_env_id`.
3. `kinsta_operation_status` on the returned operation id, repeatedly, until it
   completes. The clone is async; it is NOT usable when the call returns.
4. Run Phase 2 hygiene from the plan doc BEFORE anyone touches the clone.

**Phase 2 hygiene is not optional and is not automatable from here.** A clone
inherits the source's credentials, transactional email settings, and payment
gateway keys. On a site running WooCommerce, Tickera and a members portal, a
fresh clone can email real patrons and can reach live Stripe keys. Per
PROJECT_RULES section 4, Claude never touches Stripe credentials - flag it and
stop; Jonathan verifies.

### Pushing

`kinsta_push_environment` takes seven required arguments, none defaulted. Three
guards run before anything reaches Kinsta: the target's `display_name` must be
confirmed against a live read, source and target must differ, and a push with
both files and db false is rejected.

**`push_files: true` + `push_db: false` is the normal shape of a deploy.**

**`push_db: true` to LIVE destroys every order, ticket and customer record
created since the source was cloned.** After Aug 10 there is no scenario where
that is routine. If someone asks for it, say what it costs before doing it, and
name which environment is about to be overwritten.

### Clearing cache

`kinsta_clear_cache` on the environment id. Reach for this when a content or CSS
change is live in WordPress but the front end still serves the old HTML - the
symptom that reads like "the edit didn't save" and isn't.

## 3. The naming trap, post-cutover

At the DNS cutover, `arsnovasingers.kinsta.cloud` becomes the public site. The
install does not change; its meaning does. Until the connectors are relabelled
(Phase 3 of the plan doc), `wp_check_environment` on `ars-nova-wordpress-kinsta`
will still report "safe to experiment" about what is by then production.

If you are working after the cutover date and that connector still says DEV,
distrust it and verify against this connector's `primaryDomain` before writing
anything.

## 4. Traps hit while building this connector (2026-08-08)

Recorded because each cost real time and none were guessable:

- **`ArsNovaSingers` is a GitHub user account, not an organization.**
  `POST /orgs/ArsNovaSingers/repos` 404s. Create repos in the account.
- **Unpinned `mcp>=1.2.0` resolved to 2.0.0**, which removed
  `mcp.server.fastmcp`. Container built, then crashed on import. All deps now
  carry major-version ceilings.
- **HTTP 421 Misdirected Request on Cloud Run** is the MCP SDK's DNS-rebinding
  protection, on by default with an empty Host allow-list. Invisible locally
  because 127.0.0.1 is exempt. `MCP_ALLOWED_HOSTS` must name the Cloud Run
  hostname.
- **406 from `/mcp?key=<token>` means healthy**, not broken - the gate passed and
  the MCP endpoint rejected a plain GET's content type. 401 means the token is
  wrong.

## 5. Boundaries

This skill does not cover: the DNS cutover
(`ANS_Launch_Infrastructure_Plan.md`), WordPress content work (`ars-nova-web`),
plugin release mechanics (`Ars_Nova_Plugin_Build_Rules.md`), or Stripe in any
form. `delete environment` and site create/delete are deliberately not exposed
by the connector - call `kinsta_capability_note` before reporting something as
impossible, since it may be a decision rather than a gap.
