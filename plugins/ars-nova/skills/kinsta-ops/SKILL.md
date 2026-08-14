---
name: kinsta-ops
description: >-
  Operate the Ars Nova site at the HOSTING layer through the Kinsta tools on the
  `ars-nova-org-mcp` connector - list sites and environments, clone an
  environment, push one environment over another, clear the Kinsta full-page
  cache, and poll async operations. Trigger on "create a staging site", "clone
  the environment", "push staging to live", "clear the Kinsta cache", "what
  environments exist", "is the staging site up", "kinsta-ops", or any request
  that needs the hosting platform rather than WordPress itself. NOT for editing
  pages, posts, plugins or theme settings - that is the `ans-wordpress-*`
  connectors (use ars-nova-web). NOT for DNS.
---

# kinsta-ops

## 0. Which layer am I acting on

Two different things can both be called "the site." Get this right before doing
anything:

| Connector | Talks to | Use it for |
|---|---|---|
| `ans-wordpress-live` | WordPress on `arsnovasingers.org` | pages, posts, plugins, users - **production** |
| `ans-wordpress-staging` | WordPress on `stg-arsnovasingers-staging.kinsta.cloud` | same, on the build environment |
| **`ars-nova-org-mcp`** | **the Kinsta hosting API** | **environments, clone, push, cache** |

This skill is the last one. Its tools are all prefixed `kinsta_`.

**Do not trust a connector's name to tell you which environment it points at.**
Names have been wrong before: an earlier version of this skill named three
connectors that no longer exist, and `wp_check_environment` reported "DEV - safe
to experiment" about production for days after the DNS cutover. Confirm with a
live read every time - `wp_ops_status` for the WordPress layer (it returns
`site` and `is_production` from the install itself), `kinsta_list_environments`
for this one.

## 1. Never state current state from memory - read it

There is deliberately no table of site ids, environment ids, domains or "what
exists today" in this skill. A previous version had one, and every line of it
was wrong within days: it recorded that no staging environment existed and that
DNS had not been cut over. Both changed, the skill did not, and a session
trusting it would have concluded the safe build environment was unavailable.

That is PROJECT_RULES §14 in one paragraph. So:

**Call `kinsta_list_sites`, then `kinsta_list_environments`, first, every time.**

It costs two calls and it is the only way to know what is actually there. Ids
are stable; the set of environments is not, and neither is which domain is
primary.

## 2. The workflow

### Cloning an environment

1. `kinsta_list_environments` - confirm what exists. Never clone blind.
2. `kinsta_clone_environment` with the source env id.
3. `kinsta_operation_status` on the returned operation id, repeatedly, until it
   completes. The clone is async and is **not** usable when the call returns.
4. Run hygiene on the clone BEFORE anyone touches it - see below.

**Clone hygiene is not optional and cannot be automated from here.** A clone
inherits the source's credentials, transactional mail settings and payment
gateway keys. On a site running WooCommerce, Tickera and a members portal, a
fresh clone can email real patrons and can reach live Stripe keys. Per
PROJECT_RULES §4, Claude never touches Stripe credentials - flag it and stop.
Jonathan verifies.

This is not hypothetical for the existing staging environment: it inherited
Live's mail and Stripe credentials, so **mail and checkout there are live-fire.**
Layout, CSS and content work on staging is safe. Anything that could send mail
or take a payment is not.

### Pushing

`kinsta_push_environment` takes seven required arguments, none defaulted. Three
guards run before anything reaches Kinsta: `confirm_target_display_name` must
match the target's real name read live from the API, source and target must
differ, and a push with both files and db false is rejected.

**`push_files: true` + `push_db: false` is the normal shape of a deploy.**

**`push_db: true` to LIVE destroys every order, ticket and customer record
created since the source was cloned.** The site is a live storefront taking real
money. There is no routine scenario for that flag. If someone asks for it, say
what it costs and name the environment about to be overwritten, before doing
anything.

### Clearing cache

`kinsta_clear_cache` on the environment id. Reach for this when a content or CSS
change is live in WordPress but the front end still serves the old HTML - the
symptom that reads like "the edit didn't save" and isn't. An edge-cached page has
already made a successful fix look like a failure once.

## 3. What this connector deliberately will not do

Call `kinsta_capability_note` before reporting a Kinsta operation as impossible.
Deleting an environment and creating or deleting a site are **withheld by
choice**, not missing by accident - adding one means editing that server and
cutting a release, which is the point. A limit you can read is not the same as a
gap.

## 4. Traps hit while building this connector (2026-08-08)

Recorded because each cost real time and none were guessable:

- **`ArsNovaSingers` is a GitHub user account, not an organization.**
  `POST /orgs/ArsNovaSingers/repos` 404s. Create repos in the account.
  (Re-confirmed 2026-08-13: `GET /user` returns `"type": "User"`.)
- **Unpinned `mcp>=1.2.0` resolved to 2.0.0**, which removed
  `mcp.server.fastmcp`. The container built, then crashed on import. Pin
  major-version ceilings on every dependency.
- **HTTP 421 Misdirected Request on Cloud Run** is the MCP SDK's DNS-rebinding
  protection, on by default with an empty Host allow-list. Invisible locally
  because 127.0.0.1 is exempt. `MCP_ALLOWED_HOSTS` must name the Cloud Run
  hostname.
- **406 from `/mcp?key=<token>` means healthy**, not broken - the gate passed and
  the MCP endpoint rejected a plain GET's content type. 401 means the token is
  wrong.
- **A 401 on `/.well-known/oauth-*` breaks the Add-connector flow** (added
  2026-08-13, from `ars-nova-github-max`). The client reads it as "this resource
  is OAuth-protected," POSTs `/register`, gets a 404, and reports "can't
  connect" - even after the `?key=` handshake already returned 200. Return
  **404** on `.well-known/*` instead.

## 5. Boundaries

This skill covers the Kinsta hosting layer only. It does not cover WordPress
content work (`ars-nova-web`), plugin release mechanics
(`Ars_Nova_Plugin_Build_Rules.md`), DNS, or Stripe in any form. It states no
current system state on purpose - see §1.
