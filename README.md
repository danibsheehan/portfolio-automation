# portfolio-automation

A single Claude Code skill that powers a scheduled cloud routine: once a week, it reads Danielle's
portfolio-linked project repos, drafts a short people-friendly summary of what changed in each,
and opens a PR (never merges it) against
[`danibsheehan.github.io`](https://github.com/danibsheehan/danibsheehan.github.io) to keep that
project's section on the portfolio site current.

This repo holds no application code of its own — it exists so the skill has exactly one home
instead of a duplicated copy inside each project repo it covers (see "Why a separate repo"
below).

## The skill

[`.cursor/skills/weekly-project-update/SKILL.md`](.cursor/skills/weekly-project-update/SKILL.md)
(`.cursor/skills` is a directory symlink to the same file — Claude Code and Cursor both read it).
It documents, in order: how to gather a repo's past week of changes, how to filter for
people-relevant signal (skipping routine dependency bumps and doc-only churn), the voice the
blurb should be written in (warm, punchy, human, dual-audience), when the portfolio page's "About
this" prose also needs a rewrite, and how to edit/PR the portfolio repo — one PR per source repo
with real signal, never merged automatically.

## The work-experience-update skill

[`.claude/skills/work-experience-update/SKILL.md`](.claude/skills/work-experience-update/SKILL.md)
takes a short note from Danielle — a new role, a promotion, a notable engineering accomplishment —
and opens a PR against `danibsheehan.github.io`'s `index.html`, updating its
`#professional-experience` section (distinct from `weekly-project-update`, which only touches
`projects/index.html`). Scoped to engineering work experience from 2016 onward only. Invokable any
time she has something to report, or in response to the monthly nudge routine's push notification
(see "Autonomy boundary" below).

## Repos currently covered

- [`danibsheehan/caught-looking`](https://github.com/danibsheehan/caught-looking)
- [`danibsheehan/musing`](https://github.com/danibsheehan/musing)
- [`danibsheehan/baseball-collection`](https://github.com/danibsheehan/baseball-collection)
- [`danibsheehan/gotta-catch-em-all`](https://github.com/danibsheehan/gotta-catch-em-all)

See the skill's "Adding a new repo to coverage" section for how to extend this list.

## Autonomy boundary

**In plain English:** this routine may open a PR against `danibsheehan.github.io` on its own,
once a week. It never merges anything, and it never writes to any of the source repos it reads
from — those are read-only inputs.

- **What runs on its own**: a scheduled Claude Code cloud routine ("Weekly portfolio update")
  clones this repo plus the four source repos above every Monday, follows
  [`weekly-project-update`](.cursor/skills/weekly-project-update/SKILL.md) step by step, and
  opens one PR per source repo with people-relevant signal that week against
  `danibsheehan.github.io`, updating that repo's project section.
- **Guardrail**: opens PRs, but never merges them. Only touches `danibsheehan.github.io`'s
  `projects/index.html`, and only the section belonging to the source repo the update came from.
  A person reviews and merges each PR by hand.
- **Why opening (not merging) is safe to automate**: opening a PR doesn't ship anything — a
  person still reviews the diff before the merge button matters. Merging is where risk lives, and
  that stays a manual, per-PR decision, always.
- **What runs on its own (monthly nudge)**: a separate scheduled cloud routine ("Monthly
  work-experience nudge") fires a push notification once a month asking if there's a work update
  to add. It takes no repo action of its own — no cloning, no reading, no writing. Danielle
  answers only when there's something to report, by invoking `work-experience-update` herself.
- **`work-experience-update`'s PR scope**: opens PRs against `danibsheehan.github.io`, but only
  ever touches `index.html`'s `#professional-experience` section — never `projects/index.html`,
  never any other part of the site. Same guardrail: opens, never merges; a person reviews and
  merges by hand.

## Why a separate repo

This skill is inherently cross-repo — it reads four different source repos and writes to a fifth.
That's different from a typical per-repo skill (like `dependabot-triage`, which each project repo
has its own independent copy of, since each instance only ever operates on the repo it lives in).
A cross-repo orchestrator like this one either needs one canonical home, or N copies that must be
kept in sync by hand every time the voice guidance or repo list changes. One home wins — this
repo is that home, referenced (never duplicated) from each source repo's own README/AGENTS.md.
