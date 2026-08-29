---
name: work-experience-update
description: >-
  Takes a short note from Danielle about a new role, promotion, or notable
  engineering accomplishment (2016-onward professional experience only) and
  opens (never merges) a PR against danibsheehan/danibsheehan.github.io
  updating the #professional-experience section of index.html. Invokable any
  time she has an update, or in response to the monthly nudge routine's push
  notification. Use when Danielle gives a short update about her job/career
  and wants the portfolio site's Experience section refreshed.
---

# Work experience update (Danielle's note → portfolio)

`danibsheehan/danibsheehan.github.io` has an `index.html` with a `#professional-experience`
section — a list of `experience-card` articles, one per role, that goes stale as Danielle's
career progresses. This skill takes a short note from her and opens a PR updating that section.
It never merges the PR it opens — see this repo's [`README.md`](../../../README.md#autonomy-boundary)
for the full autonomy boundary this skill operates under.

This is a different target than [`weekly-project-update`](../weekly-project-update/SKILL.md),
which only touches `projects/index.html`. This skill only touches `index.html`'s
`#professional-experience` article(s) — never `projects/index.html`, never any other section of
`index.html`.

## Scope filter

Only engineering work experience from 2016 onward qualifies — matching the three roles already on
the site (Perceptyx, CultureIQ, Forbes, all 2016+). If Danielle's note describes something outside
that scope (pre-2016 experience, a non-engineering role), stop and say so plainly rather than
drafting anything.

## Order of work

### 1. Take the note as-is

The input is whatever short note Danielle gives — a new role, a promotion, a notable
accomplishment. No fetching, no LinkedIn access, no research beyond what she says. Don't ask her
to write more than a sentence or two; this skill's job is to expand a short note into a properly
voiced card, not to demand a full write-up.

### 2. Classify the note

- **New role at a new company** → a new `experience-card` article.
- **Update to an existing role** (promotion, expanded scope, extended dates) → edit that role's
  existing `experience-card` article's `experience__dates` and/or `experience__body` in place.

### 2a. New company logo

For a new company, don't block on this. Draft the full card immediately: omit the `img` tag (or
leave the `experience__logo--<slug>` class stubbed with no `src`) and insert an HTML comment in
its place, e.g. `<!-- TODO: add Cloudinary-hosted logo for <Company> -->`, so it's a drop-in once
supplied. Separately, nudge Danielle for a Cloudinary-hosted logo URL (matching the existing
`res.cloudinary.com` pattern used by the other cards) — mention it in the same turn and in the PR
description, but never let it block opening the PR.

### 3. Draft the card content

First-person voice matching the existing three bios' tone — concrete outcomes, team sizes, named
initiatives (e.g. "Over 4.5 years, I led two engineering teams — 11 engineers in total — building
self-service and AI-powered products I'm genuinely proud of."). Warm but factual, not a resume
bullet list and not marketing copy. Reuse the existing `experience-card` / `experience__company` /
`experience__position` / `experience__dates` / `experience__description` / `experience__body` BEM
class names exactly — check `assets/css/` for the existing styling before adding anything new.

### 4. Placement

The `experience-list--rail` list is newest-first (current order: Perceptyx 2021–2026, CultureIQ
2020–2021, Forbes 2016–2019). A new role's card goes at the top of that list. An update to an
existing role edits that card in place; it does not move.

### 5. Branch, edit, PR

```bash
gh repo clone danibsheehan/danibsheehan.github.io /tmp/danibsheehan-site -- --depth 1
```

Branch off `master` (e.g. `experience-update/<company-slug>`), edit only the relevant
`#professional-experience` article(s) in `index.html` — nothing else in the file, nothing in
`projects/index.html`. Open the PR with `gh pr create`, why-first description (what changed and
why it's going on the site), noting in the description if a logo TODO is still outstanding.

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

## Reference

- Target section: `index.html`'s `#professional-experience` in `danibsheehan/danibsheehan.github.io`
  — the `experience-list--rail` list of `experience-card` articles.
- Autonomy boundary: this repo's [`README.md`](../../../README.md#autonomy-boundary) — "opens,
  never merges" is the guardrail for this skill too.
