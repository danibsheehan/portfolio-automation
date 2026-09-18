---
name: systems-page-audit
description: >-
  Monthly audit comparing danibsheehan/danibsheehan.github.io's Systems & Automation page
  (systems/index.html) against the live current state of the three infrastructure repos it
  describes — dani-foundations, dani-actions, and this repo (portfolio-automation) — and opens
  (never merges) a correction PR against danibsheehan.github.io wherever a panel's claim has
  drifted from reality. Independent of weekly-project-update and work-experience-update, which
  only touch projects/index.html and about/index.html respectively. Use for the monthly
  systems-page audit, or when asked whether the Systems page still accurately describes
  dani-foundations, dani-actions, or this repo's own skills.
---

# Systems page audit (infrastructure repos → portfolio)

`danibsheehan/danibsheehan.github.io` has a `systems/index.html` page ("Systems & Automation")
describing three pieces of Danielle's development infrastructure, each as its own
`.pipeline__detail-panel`: `dani-foundations` (a shared Claude Code skills toolbox),
`dani-actions` (reusable GitHub Actions workflows), and `portfolio-automation` — this repo. Each
panel's prose (skill/workflow counts, example-standard bullets, "Why it matters" claims) describes
a real, current state that can quietly drift as those repos evolve. This skill re-reads each
panel against its actual source of truth and opens a correction PR only where something has
genuinely gone stale. It never writes to any of the three source repos — only to
`danibsheehan.github.io`. See this repo's [`README.md`](../../../README.md#autonomy-boundary) for
the full autonomy boundary this skill operates under.

This is a different target than [`weekly-project-update`](../weekly-project-update/SKILL.md)
(`projects/index.html`) and [`work-experience-update`](../work-experience-update/SKILL.md)
(`about/index.html`'s `#changelog`). This skill only touches `systems/index.html`'s three
detail panels — never any other page or section.

## Panels covered

| Panel id | Source of truth | What's audited |
| --- | --- | --- |
| `detail-foundations` | `danibsheehan/dani-foundations` | Skill count, the example-standard bullets, the "Why it matters" claim |
| `detail-actions` | `danibsheehan/dani-actions` | Workflow/action count, the example-standard bullets, the "Why it matters" claim |
| `detail-automation` | This repo (`danibsheehan/portfolio-automation`) — its own `.claude/skills/*/SKILL.md` files | The description of each routine's cadence, target, and behavior; the "Why it matters" claim |

Run every panel below **independently** — drift in one panel never implies drift in another.

## Order of work

For each panel in the table above, independently:

1. Read that panel's current prose on the live page (`systems/index.html`'s
   `#detail-<panel>` block in `danibsheehan/danibsheehan.github.io`).
2. Read the actual current state of that panel's source of truth:
   - `detail-foundations` / `detail-actions`: that repo's own README, and (where the panel makes a
     countable claim, e.g. "25 Claude Code skills") the actual count of skill directories or
     workflow files in that repo — don't trust a stale README number over the repo's real
     contents.
   - `detail-automation`: this repo's own `.claude/skills/*/SKILL.md` files — what each routine's
     frontmatter `description` and body actually say it does, today.
3. Compare claim by claim: does every count, every example-standard bullet, and the "Why it
   matters" line still hold? Flag anything wrong, outdated, or describing a standard/workflow/skill
   that no longer exists (or exists but now works differently).
4. Nothing drifted for that panel → do nothing, no PR. Don't manufacture an audit finding just to
   have something to show for the pass.
5. Something drifted → branch off `master` fresh (a separate branch per panel with signal, e.g.
   `systems-audit/<panel-id>`) and open one PR for that panel, correcting only the specific stale
   claim(s) — don't rewrite the panel wholesale unless multiple things are simultaneously wrong,
   and don't touch a panel with nothing wrong just because another panel needed a fix.

Each PR touches only its own panel — the three panels live in non-overlapping parts of the same
file, so independent PRs from independent branches merge cleanly regardless of order.

## Open the PR — never merge it

```bash
gh pr create --repo danibsheehan/danibsheehan.github.io --title "..." --body "..."
```

Why-first PR description: what's actually wrong in that panel and what the correction says now,
in plain language. **Do not merge any of these PRs.** Merging is always a manual, separate
decision — this skill's job ends at opening them.

## Anti-patterns

- Editing anything in `dani-foundations`, `dani-actions`, or this repo itself — this skill only
  reads those repos; it writes only to `danibsheehan.github.io`.
- Merging a portfolio PR, or leaving one in a state that looks pre-approved.
- Rewriting a panel wholesale for one drifted claim — fix only what's actually wrong.
- Manufacturing an audit finding for a panel that hasn't actually drifted, just to have something
  to show for a given month's pass.
- Blending drift from two panels into one PR, or touching a panel that has nothing wrong because
  another panel in the same file needed a fix.
- Inventing or embellishing a correction beyond what the source of truth actually supports.

## Reference

- Target page: `systems/index.html` in `danibsheehan/danibsheehan.github.io` — see the "Panels
  covered" table above for each panel's id and source of truth.
- Autonomy boundary: this repo's [`README.md`](../../../README.md#autonomy-boundary) — "opens,
  never merges" is the guardrail for this skill too.
