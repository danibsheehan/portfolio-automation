---
name: automation-pulse-refresh
description: >-
  Weekly refresh of danibsheehan/danibsheehan.github.io's assets/data/automation-pulse.json —
  the data file behind the Systems page's "recent activity" snapshot — with each of this repo's
  routines' most recent PRs against that site. Read-only against danibsheehan.github.io's own
  PR history; no project-repo checkouts needed. Opens (never merges) a single PR per run. Use
  for the weekly automation-pulse refresh, or when asked whether the Systems page's recent-
  activity snapshot is current.
---

# Automation pulse refresh (this repo's own PR history → portfolio data file)

`danibsheehan/danibsheehan.github.io` has a `systems/index.html` page whose
`detail-automation` panel renders a small "recent activity" snapshot — the last few PRs each
of this repo's routines opened against that site — from `assets/data/automation-pulse.json`
(consumed via `assets/systems-diagram.js`). That file is currently hand-seeded and explicitly
labeled in place as a placeholder ("Not a live feed — refreshed manually until a scheduled
snapshot step is wired up in portfolio-automation"). This skill is that scheduled step: it
reads `danibsheehan.github.io`'s own PR history (nothing else) and rebuilds the snapshot. It
never writes to any project repo, `dani-foundations`, or `dani-actions` — only to
`danibsheehan.github.io`'s data file. See this repo's
[`README.md`](../../../README.md#autonomy-boundary) for the full autonomy boundary this skill
operates under.

This is a different job from every other skill in this repo: it doesn't draft or correct
portfolio prose at all — it only refreshes structured PR metadata. It never touches
`projects/index.html`, `about/index.html`, or `systems/index.html`'s prose (that last one is
[`systems-page-audit`](../systems-page-audit/SKILL.md)'s job).

## The data file

`assets/data/automation-pulse.json` in `danibsheehan.github.io`:

```json
{
  "snapshotNote": "...",
  "routines": [
    {
      "name": "<routine name>",
      "cadence": "<cadence string>",
      "targetRepo": "danibsheehan.github.io",
      "recentPRs": [
        { "number": 0, "title": "...", "date": "YYYY-MM-DD", "url": "..." }
      ]
    }
  ]
}
```

One `routines` entry per routine that opens PRs against `danibsheehan.github.io`:
`weekly-project-update`, `weekly-project-update` (monthly accuracy audit), `work-experience-
update`, and `systems-page-audit`. Each `recentPRs` list holds at most the 3 most recent PRs
for that routine, newest first.

## Order of work

1. For each routine, gather its most recent merged (or open, if not yet merged) PRs against
   `danibsheehan/danibsheehan.github.io` by branch-name prefix:
   ```bash
   gh pr list --repo danibsheehan/danibsheehan.github.io --state all --search "head:weekly-update/"
   gh pr list --repo danibsheehan/danibsheehan.github.io --state all --search "head:monthly-audit/"
   gh pr list --repo danibsheehan/danibsheehan.github.io --state all --search "head:experience-update/"
   gh pr list --repo danibsheehan/danibsheehan.github.io --state all --search "head:systems-audit/"
   ```
   Take each result's `number`, `title`, `url`, and merge/creation date; sort newest first; keep
   the top 3 per routine.
2. Rebuild `assets/data/automation-pulse.json` in place with the four `routines` entries above.
   Update `snapshotNote` to describe it as a scheduled snapshot (drop the "refreshed manually
   until wired up" wording once this skill is the thing doing the wiring) but keep it honest that
   it's still a periodic snapshot, not a live feed.
3. If a routine has no PRs yet (e.g. a newly added one, or `work-experience-update` in a quiet
   stretch), its `recentPRs` stays an empty array — don't fabricate an entry.

## Open the PR — never merge it

```bash
gh pr create --repo danibsheehan/danibsheehan.github.io --title "..." --body "..."
```

Single branch, single PR per run (e.g. `automation-pulse-refresh/<date>`) — this is one data
file, not a per-repo or per-panel change. Why-first PR description: which routines' snapshots
changed and why (new PRs since the last refresh). **Do not merge this PR.** Merging is always a
manual, separate decision — this skill's job ends at opening it.

## Anti-patterns

- Editing anything in a project repo, `dani-foundations`, `dani-actions`, or this repo itself —
  this skill only reads `danibsheehan.github.io`'s own PR history and writes only to its
  `assets/data/automation-pulse.json`.
- Touching `systems/index.html`'s prose, or any other page/section — that's `systems-page-
  audit`'s job (for the Systems page) or the other skills' jobs (for their own pages).
- Inventing or backfilling PR data that doesn't reflect real `gh pr list` results.
- Merging the PR, or leaving it in a state that looks pre-approved.
- Opening more than one PR per run, or splitting the JSON rebuild across multiple PRs — it's one
  file, one PR.

## Reference

- Target file: `assets/data/automation-pulse.json` in `danibsheehan/danibsheehan.github.io`,
  consumed by `systems/index.html` via `assets/systems-diagram.js`.
- Autonomy boundary: this repo's [`README.md`](../../../README.md#autonomy-boundary) — "opens,
  never merges" is the guardrail for this skill too.
