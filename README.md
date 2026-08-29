# portfolio-automation

Claude Code skills that power scheduled cloud routines keeping
[`danibsheehan.github.io`](https://github.com/danibsheehan/danibsheehan.github.io) current: one
reads Danielle's portfolio-linked project repos every week and drafts a short people-friendly
summary of what changed in each; the other takes a short note from her about a new role,
promotion, or notable engineering accomplishment and drafts an update to her Experience section.
Both open a PR (never merge it) against `danibsheehan.github.io` to make the change.

This repo holds no application code of its own — it exists so these skills have exactly one home
instead of a duplicated copy inside each project repo they cover (see "Why a separate repo"
below).

## Skills

### weekly-project-update

[`.claude/skills/weekly-project-update/SKILL.md`](.claude/skills/weekly-project-update/SKILL.md)
(`.cursor/skills` is a directory symlink to the same file — Claude Code and Cursor both read it).
It documents, in order: how to gather a repo's past week of changes, how to filter for
people-relevant signal (skipping routine dependency bumps and doc-only churn), the voice the
blurb should be written in (warm, punchy, human, dual-audience), when the portfolio page's "About
this" prose also needs a rewrite, and how to edit/PR the portfolio repo — one PR per source repo
with real signal, never merged automatically.

**Repos currently covered:**

- [`danibsheehan/caught-looking`](https://github.com/danibsheehan/caught-looking)
- [`danibsheehan/musing`](https://github.com/danibsheehan/musing)
- [`danibsheehan/baseball-collection`](https://github.com/danibsheehan/baseball-collection)
- [`danibsheehan/gotta-catch-em-all`](https://github.com/danibsheehan/gotta-catch-em-all)

See the skill's "Adding a new repo to coverage" section for how to extend this list.

### work-experience-update

[`.claude/skills/work-experience-update/SKILL.md`](.claude/skills/work-experience-update/SKILL.md)
takes a short note from Danielle — a new role, a promotion, a notable engineering accomplishment —
and opens a PR against `danibsheehan.github.io`'s `index.html`, updating its
`#professional-experience` section (distinct from `weekly-project-update`, which only touches
`projects/index.html`). Scoped to engineering work experience from 2016 onward only. Invokable any
time she has something to report, or in response to the monthly nudge routine's push notification
(see "Autonomy boundary" below).

## Autonomy boundary

**In plain English:** these routines may open PRs against `danibsheehan.github.io` on their own.
They never merge anything, and `weekly-project-update` never writes to any of the source repos it
reads from — those are read-only inputs. Opening a PR doesn't ship anything — a person still
reviews the diff before the merge button matters. Merging is where risk lives, and that stays a
manual, per-PR decision, always.

| Skill | Trigger | Writes to | Guardrail |
|---|---|---|---|
| `weekly-project-update` | Scheduled cloud routine ("Weekly portfolio update") runs every Monday, cloning this repo plus the four source repos and following the skill step by step | `danibsheehan.github.io`'s `projects/index.html` — only the section belonging to the source repo the update came from | Opens one PR per source repo with signal that week; never merges — a person reviews and merges by hand |
| `work-experience-update` | Danielle invokes it herself whenever she has something to report. A separate scheduled routine ("Monthly work-experience nudge") only sends a push notification reminder once a month — it takes no repo action of its own | `danibsheehan.github.io`'s `index.html`, `#professional-experience` section only — never `projects/index.html` | Opens a PR; never merges — a person reviews and merges by hand |

## Why a separate repo

`weekly-project-update` is inherently cross-repo — it reads four different source repos and writes
to a fifth. That's different from a typical per-repo skill (like `dependabot-triage`, which each
project repo has its own independent copy of, since each instance only ever operates on the repo it
lives in). A cross-repo orchestrator like this one either needs one canonical home, or N copies that
must be kept in sync by hand every time the voice guidance or repo list changes. `work-experience-update`
doesn't read multiple source repos itself, but it shares this home too, so both skills that write to
`danibsheehan.github.io` live in one canonical place instead of being split across repos. One home
wins — this repo is that home, referenced (never duplicated) from each source repo's own
README/AGENTS.md.
