# AGENTS.md

Instructions for any coding agent (Claude Code, Cursor, or otherwise) working in this repo.
Human contributors: see [`README.md`](README.md) instead — this file is written for agents and
skips the narrative tour.

This repo holds no application code of its own. It's a single Claude Code skill that powers a
scheduled cloud routine, keeping [`danibsheehan.github.io`](https://github.com/danibsheehan/danibsheehan.github.io)
current with what's changed across Danielle's portfolio-linked project repos.

## Autonomy boundary

This routine opens PRs against `danibsheehan.github.io` on its own; it never merges them, and
never writes to any of the source repos it reads from. Full boundary:
[`README.md`](README.md#autonomy-boundary) — do not restate or duplicate it here; that section is
the single source of truth.

## Skills

`.claude/skills/*/SKILL.md` are the canonical playbooks. `.cursor/skills` is a directory symlink
to `.claude/skills` — same files, no copies.

### Working on this repo

When making changes to this repo's own files (SKILL.md playbooks, README, AGENTS.md/CLAUDE.md),
use these `dani-foundations` skills:

- `branch-naming` — for branch names (`<type>/<slug>`, Conventional-Commits-style prefixes).
- `pr-chunk-plan` — before implementation, when a task touches 3+ files or bundles multiple
  distinct concerns.
- `pr-stack-ship` — to execute a `pr-chunk-plan` breakdown as a stack of small branches/PRs,
  retargeting later PRs as earlier ones in the stack merge.
- `pr-ready` — before opening a PR, to run this repo's local checks and prepare the PR.
- `pr-summary-draft` — to draft the PR description from the actual diff/commits on the branch.

These govern work on this repo's own files only. They do not apply to the PRs
`weekly-project-update` and `work-experience-update` open against `danibsheehan.github.io` —
those skills fully specify their own branch/PR mechanics (see each skill's own file and the
README's [autonomy boundary](README.md#autonomy-boundary)).

## Repos covered

See the `weekly-project-update` skill's own "Repos covered" table and "Adding a new repo to
coverage" section for the current list and how to extend it — not duplicated here.
