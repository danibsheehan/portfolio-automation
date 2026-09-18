---
name: work-experience-update
description: >-
  Takes a short note from Danielle about a new role, promotion, or notable
  engineering accomplishment (2016-onward professional experience only) and
  opens (never merges) a PR against danibsheehan/danibsheehan.github.io
  updating the #changelog section of about/index.html. Invokable any
  time she has an update, or in response to the monthly nudge routine's push
  notification. Use when Danielle gives a short update about her job/career
  and wants the portfolio site's Experience section refreshed.
---

# Work experience update (Danielle's note → portfolio)

`danibsheehan/danibsheehan.github.io` has an `about/index.html` with a `#changelog` section — a
changelog-styled `<ol class="experience-timeline" id="experience-story-rail-list">` of
`experience-timeline__item` entries, one per role, that goes stale as Danielle's career
progresses. Each entry is an `<details class="experience-card experience">`: a `<summary
class="experience__summary">` holding the logo, version, company, position, and dates, followed
by an `<div class="experience__description">` made of one or more `.changelog-group` blocks
(`changelog-label--added` / `changelog-label--changed` / `changelog-label--fixed` /
`changelog-label--notes`, each with a `<ul class="prose-list">` of bullets). This skill takes a
short note from her and opens a PR updating that section. It never merges the PR it opens — see
this repo's [`README.md`](../../../README.md#autonomy-boundary) for the full autonomy boundary
this skill operates under.

This is a different target than [`weekly-project-update`](../weekly-project-update/SKILL.md),
which only touches `projects/index.html`. This skill only touches `about/index.html`'s
`#changelog` article(s) — never `projects/index.html`, never any other section of `about/index.html`
or of `index.html`.

## Scope filter

Only engineering work experience from 2016 onward qualifies — matching the three roles already on
the site (Perceptyx, CultureIQ, Forbes, all 2016+). The changelog also has a pre-existing
"Foundations" entry (`v0.1.0`, 2013–2016) that predates this scope — it's already correctly out
of bounds and not an example to follow. If Danielle's note describes something outside scope
(pre-2016 experience, a non-engineering role), stop and say so plainly rather than drafting
anything.

## Order of work

### 1. Take the note as-is

The input is whatever short note Danielle gives — a new role, a promotion, a notable
accomplishment. No fetching, no LinkedIn access, no research beyond what she says. Don't ask her
to write more than a sentence or two; this skill's job is to expand a short note into a properly
voiced card, not to demand a full write-up.

### 2. Classify the note

- **New role at a new company** → a new `<li class="experience-timeline__item" id="<company-
  slug>">` containing a new `experience-card`, inserted at the top of `#experience-story-rail-
  list`.
- **Update to an existing role** (promotion, expanded scope, extended dates) → update that
  card's `experience__dates` if applicable, and append a new `.changelog-group` to its
  `experience__description` (or new bullets to the single most-fitting existing group — judgment
  call based on what the note describes) rather than editing or replacing prior bullets. The card
  stays where it is; it does not move.

### 2a. New company logo

For a new company, don't block on this. Draft the full card immediately: omit the `img` tag (or
leave the `experience__logo experience__logo--<company-slug>` classes stubbed with no `src`) and
insert an HTML comment in its place, e.g. `<!-- TODO: add Cloudinary-hosted logo for <Company>
-->`, so it's a drop-in once supplied. Separately, nudge Danielle for a Cloudinary-hosted logo URL
(matching the existing `res.cloudinary.com` pattern used by the other cards) — mention it in the
same turn and in the PR description, but never let it block opening the PR.

### 2b. Version number

A new company's card gets the next `experience__version` up from whatever's currently highest on
the page (e.g. if the newest existing card is `v3.0.0`, the new one is `v4.0.0`). An update to an
existing role never changes that role's version.

### 3. Draft the card content

Third-person, terse, resume-bullet-fragment voice matching the existing cards' tone — no "I",
concrete outcomes, team sizes, named initiatives, past-tense verb leading each bullet (e.g.
"Directed a greenfield AI-powered product from architecture through launch in 6 months," "Grew a
full-stack team across React/TypeScript, Vue/TypeScript, Go, and AWS — two teams, 11 engineers in
total"). Warm but factual, not marketing copy.

Group bullets into `.changelog-group` blocks by judgment, not a fixed template — not every group
needs to be populated for a short note:

- **Added** (`changelog-label--added`) — new ownership, initiatives, or products started.
- **Changed** (`changelog-label--changed`) — scope, team, or role shifts.
- **Fixed** (`changelog-label--fixed`) — stabilization, process fixes, mentoring outcomes.
- **Notes** (`changelog-label--notes`, optional) — talks, press, external links.

Reuse the existing `experience-card` / `experience__company` / `experience__position` /
`experience__dates` / `experience__description` / `changelog-group` / `changelog-label` /
`prose-list` BEM class names exactly — check `assets/css/` for the existing styling before adding
anything new.

### 4. Placement

The `experience-story-rail-list` list (id on the `<ol class="experience-timeline">`) is
newest-first, expressed via descending `experience__version` (current order: Perceptyx `v3.0.0`
2021–2026, CultureIQ `v2.0.0` 2020–2021, Forbes `v1.0.0` 2016–2019). A new role's card goes at the
top of that list with the next version up (see step 2b). An update to an existing role edits that
card's `<li class="experience-timeline__item" id="<company-slug>">` in place; it does not move.
Note the stable `id` lives on that `<li>` wrapper, not on the `<details>` element inside it.

### 5. Branch, edit, PR

```bash
gh repo clone danibsheehan/danibsheehan.github.io /tmp/danibsheehan-site -- --depth 1
```

Branch off `master` (e.g. `experience-update/<company-slug>`), edit only the relevant `#changelog`
article(s) in `about/index.html` — nothing else in the file, nothing in `projects/index.html`.
Open the PR with `gh pr create`, why-first description (what changed and why it's going on the
site), noting in the description if a logo TODO is still outstanding.

### 6. Never merge

Same as every other skill in this repo: opening a PR doesn't ship anything, a person still
reviews the diff. **Do not merge or approve the PR.** That decision is always manual.

## Anti-patterns

- Touching `projects/index.html` — that's `weekly-project-update`'s territory, not this skill's.
- Drafting a card for pre-2016 or non-engineering experience.
- Fabricating or guessing a Cloudinary logo URL instead of leaving the TODO comment and asking.
- Blocking card drafting on the logo being supplied.
- Merging or approving the PR, or leaving it in a state that looks pre-approved.
- Inventing or embellishing accomplishments beyond what Danielle's note actually said.
- Reordering or editing any existing card beyond what the note calls for.
- Mutating or replacing an existing role's prior `changelog-group` bullets when the note is really
  an *update* to that role — append a new group or new bullets instead (step 2).

## Reference

- Target section: `about/index.html`'s `#changelog` in `danibsheehan/danibsheehan.github.io` —
  the `experience-story-rail-list` list of `experience-timeline__item` entries, each an
  `experience-card` with `changelog-group` bullets.
- Autonomy boundary: this repo's [`README.md`](../../../README.md#autonomy-boundary) — "opens,
  never merges" is the guardrail for this skill too.
