---
name: ans-plugin-publisher
description: Ship a new or updated skill (or connector/tool) to the Ars Nova Claude plugin the right way, so the whole team gets it. Handles the full GitHub flow — branch, commit/push, pull request, squash-merge, version bump, branch cleanup — against the ArsNovaSingers/ars-nova-claude-plugin repo, and explains each step in plain language. Trigger on "publish the plugin", "ship this skill", "update the Ars Nova plugin", "add this skill to the plugin", "release a new plugin version", "ans-plugin-publisher", or any time a skill/connector needs to land in the shared plugin. NOT for building the skill's content itself (author that first), and NOT for website/WordPress plugins (use ars-nova-web).
---

# Ars Nova — Plugin Publisher

Get a change into the shared **Ars Nova Claude plugin** cleanly and repeatably. Jonathan is not a git expert — narrate each step in plain terms and never assume prior GitHub knowledge.

## The repo
- **`ArsNovaSingers/ars-nova-claude-plugin`** (default branch **`main`**), reached via the **`ars-nova-github`** connector (a classic token with the `repo` scope; if a write returns 403 "resource not accessible", the token/permission is the problem, not the code — see Troubleshooting).
- Layout:
  - `.claude-plugin/marketplace.json` — the marketplace manifest (has the plugin `version`).
  - `plugins/ars-nova/.claude-plugin/plugin.json` — the plugin manifest (has the plugin `version`).
  - `plugins/ars-nova/skills/<skill-name>/SKILL.md` — one folder per skill.

## Plain-language glossary
- **Branch** = a scratch copy to edit so `main` is never half-done.
- **Commit / push** = save edits onto the branch.
- **Pull request (PR)** = "here's my diff, please look" — the review gate.
- **Merge** = fold the PR into `main` (the version the team installs).
- **Version bump** = raise the number in both manifests so the team knows to update.
- **Delete branch** = tidy the scratch copy after merging.

## Before you publish (prep)
1. The skill content must already exist and follow conventions: `SKILL.md` with YAML front matter (`name`, `description`), a **connector firewall** block, locate-shared-files-by-name, and cross-references to sibling skills. One skill per folder.
2. **Never commit secrets** — tokens/keys live in each person's local `claude_desktop_config.json`, never in this repo.
3. Decide the **version bump** (semver), bumping BOTH manifests to the same number:
   - new skill / new capability → **minor** (1.1.0 → 1.2.0)
   - wording/typo/small fix → **patch** (1.2.0 → 1.2.1)
   - a change that breaks how an existing skill behaves → **major** (1.2.0 → 2.0.0)

## The publish flow
1. **Branch** — `create_branch` from `main`, name it for the change (e.g. `add-<skill>` or `update-<skill>`).
2. **Push** — `push_files` in ONE commit to that branch: the skill file(s) at `plugins/ars-nova/skills/<name>/SKILL.md` **plus** both bumped manifests. Clear commit message ("Add <skill>; bump plugin to vX.Y.Z").
3. **PR** — `create_pull_request` (head = your branch, base = `main`) with a body that says what changed and why. Show Jonathan the PR link and, in one line, what merging will do.
4. **Merge** — on his OK, `merge_pull_request` with `merge_method: "squash"` (one tidy commit on `main`).
5. **Cleanup** — delete the merged branch if a delete tool is available; otherwise note it can be removed in GitHub. Harmless if left.
6. **Adopt** — tell the team the new version is live and to **update the plugin** from the marketplace to receive it; to test it yourself, refresh your own plugin.

## Best practices
- **Always use a PR**, even solo — it gives a diff to review and a record, and it's how the team-facing plugin should change. Avoid committing straight to `main`.
- **Squash-merge** to keep history readable; one commit per change.
- **Bump both manifests together** — a mismatch confuses the marketplace.
- Keep changes **small and single-purpose** per PR (one skill / one fix), so the diff is easy to read.
- If you edited a skill's behavior, say so in the PR body so reviewers know what to re-test.

## Troubleshooting
- **403 "Resource not accessible by personal access token"** on any write → the `ars-nova-github` token lacks write, or it's the wrong/old token. Fix: a **classic** token with the `repo` scope, pasted into the `ars-nova-github` connector's `GITHUB_PERSONAL_ACCESS_TOKEN` in `claude_desktop_config.json`, then **restart Claude Desktop** (the connector only reads its token at launch). Use `mcp-config-manager` to edit the config safely.
- **Merge conflict** → someone else changed the same lines; pull the latest, re-apply, and re-push (ask for help rather than force-anything).

## Related skills / refs
- `ans-task-runner`, `ans-sop-writer`, `season-pm`, `project-secretary`, `ars-nova-web` — the skills that live in this plugin.
- `mcp-config-manager` — safe edits to `claude_desktop_config.json` (token swaps).
- `create-cowork-plugin` / `cowork-plugin` — scaffolding/other plugin edits.
