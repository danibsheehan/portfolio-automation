---
name: weekly-project-update
description: >-
  Summarizes the past week's changes across Danielle's portfolio-linked repos
  (caught-looking, musing, baseball-collection, gotta-catch-em-all) in a warm, punchy,
  dual-audience voice and opens (never merges) one PR per repo with real signal
  against danibsheehan/danibsheehan.github.io, updating that repo's project section —
  a "Recent updates" blurb, the Pitch or Stack card text when the week included
  something structurally notable or tech-stack-changing, a correction to the
  "Automation and AI" bullets when this week's change makes one of them factually
  stale, and (past a staleness threshold) a refresh of a "Recent updates" block left
  stale by several quiet weeks in a row. Also covers a separate monthly accuracy-audit
  pass that checks the Pitch, Stack, and "Automation and AI" text against each repo's
  current ground truth, independent of any week's diff. Use for the weekly
  portfolio-update routine, the monthly accuracy audit, or when asked to summarize
  recent changes in any of these repos for the portfolio site.
---

# Weekly project update (portfolio-linked repos → portfolio)

`danibsheehan/danibsheehan.github.io` has a static project section per repo
(`projects/index.html`) that goes stale as each app evolves. This skill drafts a short summary
of what changed in each *source* repo over the past week and opens a PR per repo (only for repos
with real signal that week) in the portfolio repo to keep those sections current. It never
touches the source repos, and it never merges any PR it opens — see this repo's
[`README.md`](../../../README.md#autonomy-boundary) for the autonomy boundary this skill
operates under.

This skill lives in its own repo, `danibsheehan/portfolio-automation`, rather than inside any one
of the repos it covers — it's cross-repo by design, so it needs exactly one home rather than a
copy per project that would drift out of sync. See the README for why, and for how to add a new
repo to its coverage.

## Repos covered

| Source repo | Portfolio section anchor | Specimen modifier class | "Recent updates" label id | "Automation and AI" label id |
| --- | --- | --- | --- | --- |
| `danibsheehan/caught-looking` | `#project-caught-looking` | `specimen--caught-looking` | `caught-looking-updates-label` | `caught-looking-automation-label` |
| `danibsheehan/musing` | `#project-musing` | `specimen--musing` | `musing-updates-label` | `musing-automation-label` |
| `danibsheehan/baseball-collection` | `#project-cartophiles` (displayed on the site as "Cartophiles" — the source repo itself is still named `baseball-collection`) | `specimen--cartophiles` | `cartophiles-updates-label` | `cartophiles-automation-label` |
| `danibsheehan/gotta-catch-em-all` | `#project-gotta-catch-em-all` | `specimen--gotta-catch-em-all` | `gotta-catch-em-all-updates-label` | `gotta-catch-em-all-automation-label` |

Run every step below **once per source repo**, independently — one repo's quiet week (skip) or
loud week (About-this rewrite) never affects another's.

## Order of work

### 1. Gather the week's changes (source repo, read-only)

```bash
gh pr list --repo <source-repo> --state merged --search "merged:>=<7-days-ago-date>" \
  --json number,title,body,mergedAt --limit 100
```

Falls back to `git log --since="7 days ago" --oneline` on that repo's default branch if `gh` PR
search comes up short (e.g. commits merged without a PR). Do not modify anything in the source
repo — this step is read-only. (A caller can widen the lookback window for a one-time backfill —
e.g. 14 days for a repo that's never had a "Recent updates" section — but the standing weekly
routine always uses 7 days.)

### 2. Filter for people-relevant signal

Skip pure dependency bumps (Dependabot version-bump titles with no accompanying story) and
doc-only drift fixes (README/rule wording, stack-doc sync) unless nothing else happened that
week — then a quieter week is fine to say plainly. Prioritize:

- **User-facing features** — new pages, new data on existing pages, UX changes.
- **Real bug fixes and performance/hardening work**, and any genuine "lesson learned" (a bug that
  revealed a wrong assumption, a fix that changed how something is cached or validated, etc).
- **AI agent usage and improvements** — a new or changed Claude Code / Cursor skill, a new
  autonomous or scheduled routine, a change to what an agent is trusted to do unattended. Each
  source repo's portfolio story is partly about *how* it's built, so agent-workflow changes are
  people-relevant even when no app code moved — don't fold them into "CI/automation tooling" and
  skip them as a chore.
- **Automation/workflow changes that speak to system design** — not every CI tweak, only the
  ones that reveal a real engineering decision: what the system now trusts to happen
  unattended (widening or narrowing Dependabot's auto-merge scope, adding a new required gate
  before code ships), or a deliberate shift in how risk or quality is managed (a new category
  of check that changes what "safe to ship" means for this codebase). The test: would an
  engineer reading the portfolio learn something about how this system is designed and
  safeguarded — not just that a config file changed? If yes, report the *policy* (what's now
  trusted to merge itself, what a new gate actually protects against), not the diff or version
  number. If no — a version-pin fix, two workflow files re-synced after drifting apart, a
  single lint rule flipped on with no larger story behind it — skip it; that's routine
  maintenance, same tier as a dependency bump.

If there's nothing worth reporting under any of the above (a quiet week — only dependency bumps,
doc wording fixes, and one-off CI/version-pin maintenance, no new or changed skills, routines, or
quality gates), check that repo's current "Recent updates" byline on the live portfolio page
before stopping:

- **Byline within the last ~3 weeks**: stop here as usual — do not open a PR for a single quiet
  week with no people-relevant signal.
- **Byline older than ~3 weeks** (several quiet weeks in a row have left the block genuinely
  stale): don't stop — open a PR anyway. Replace the blurb with a brief, honest line that nothing
  major happened (e.g. "Quiet stretch here — steady maintenance in the background, nothing
  structural to report this time.") and refresh the byline to this run's date. This is the one
  case where a week with no real signal still gets a PR; it exists solely to keep the byline
  honest about how recent "recent" is, not to manufacture a story — don't dress a no-news week up
  as more than it is.

### 3. Draft the blurb

2-4 sentences. Voice:

- **Warm and friendly** — write like a person genuinely excited about the thing they built, not
  a changelog bot. Match the tone already used in that repo's existing project-section prose on
  the portfolio site, e.g. "It's a work in progress, but already a solid sandbox for anyone who
  wants to know why a team is winning, not just that they are."
- **Punchy** — short sentences land harder than a compound one padded with qualifiers. Cut hedges
  ("essentially," "basically," "in order to"). One well-placed short sentence can be a full beat
  on its own.
- **Human, not AI-slop** — no PR numbers, no commit hashes, no listicle throat-clearing ("In this
  update, we..."), no empty superlatives ("game-changing," "seamless," "robust"), no em-dash-heavy
  triplets. Say the specific true thing that happened, not a generic shape a sentence like this
  tends to take.
- **Dual-audience, not lowest-common-denominator** — this portfolio is read by non-technical
  visitors and by engineers sizing up the work. Don't strip out real technical detail; *translate*
  it so both readers get something. Name the actual mechanism when it's the interesting part (a
  caching layer, a race condition, a rate limit), then earn it with a plain-language reason a
  non-engineer still follows — e.g. "fixed a bug where two people loading the same game at once
  could get served stale data" beats both "fixed a caching bug" (too vague for an engineer) and
  "fixed a singleflight race in the TTL cache" (opaque to everyone else).

### 4. Decide if the Pitch or Stack card needs a rewrite

Two separate, narrower triggers replaced the old single "About this" rewrite:

- **4a. Pitch** (`.specimen__pitch`, inside that project's `.specimen__header` — no id; select it
  via the project's `specimen--<slug>` modifier class in the table above). Only rewrite this when
  the week included something structurally notable — a new major feature, a real architecture
  change (e.g. a new data source, a new page). Routine fixes, perf tuning, and dependency work
  never trigger this; leave it alone in an ordinary week.
- **4b. Stack card** (`.specimen__card--stack .specimen__card-text`, inside `.specimen__cards`).
  Only rewrite this when the week's signal is an actual tech-stack change (a new framework,
  language, or major dependency swap) — not a routine version bump. If a stack change breaks a
  cross-project callout in that same card's `.specimen__thread` paragraph (e.g. "Same React 19
  frontend stack as Musing"), fix that one line too; otherwise leave `.specimen__thread` alone.

### 5. Check the "Automation and AI" section for drift

That section (`.specimen__card--automation`, its bullets in
`ul.specimen__list[data-disclosure-list]`) is an inventory of the repo's actual standing
automation (what auto-merges, what a scheduled agent or check does) — it is not a running log, so
it does not get a weekly rewrite. But when this week's signal (step 2) changes something that
section already describes — an auto-merge scope widened or narrowed, a scheduled routine's
behavior changed — check whether the existing bullets are still true. Fix only the specific
bullet(s) that are now stale, in the same PR as the "Recent updates" edit; don't rewrite the
section wholesale, and don't add a bullet for something that isn't a standing, ongoing piece of
automation (a one-off fix doesn't earn a bullet just because it's this week's "Recent updates"
story). A quiet week, or a week whose signal doesn't touch anything this section already claims,
leaves it untouched.

Some bullets are visually collapsed behind a "Show N more" disclosure toggle (marked
`hidden data-disclosure-item="hidden"`) rather than shown by default — a stale bullet may be
either kind. Edit its text in place regardless of which; don't touch the `hidden` attribute or the
toggle button's "Show N more" count unless you're actually adding or removing a bullet (which
these rules already restrict to genuine drift correction, not routine weeks).

### 6. Edit the portfolio repo — one branch and PR per repo with signal

```bash
gh repo clone danibsheehan/danibsheehan.github.io /tmp/danibsheehan-site -- --depth 1
```

For each source repo that had people-relevant signal (step 2), branch off `master` fresh (a
separate branch per source repo, e.g. `weekly-update/<source-repo-slug>`) and edit
`projects/index.html`:

- Add or replace the "Recent updates" block inside that repo's `.specimen__card--updates` card
  (see the table above for the project's anchor and label id). It holds **only the current week's
  blurb** — replace it in place each run, never append to a growing list. Reuse that project's
  existing `.specimen__cards` markup exactly (copy the block from an existing project article
  rather than inventing new markup or CSS — see the authoring comment near the top of
  `projects/index.html` for the canonical shape).
- Immediately after that card's `<p class="specimen__card-label" id="<slug>-updates-label">Recent
  updates</p>` and before its `<p class="specimen__card-text" ...>` blurb, add or replace a
  `<p class="specimen__byline">Week of <Mon Day, Year></p>` line (e.g. `Week of Aug 31, 2026`)
  using this run's date — this is the reader's only cue for how recent "recent" is, so it must be
  replaced every run, never left stale from a prior week. The `specimen__byline` class already
  exists in `assets/css/app.css`; reuse it as-is.
- If step 4a said yes for that repo, revise its `.specimen__pitch` paragraph in the same PR.
- If step 4b said yes for that repo, revise its `.specimen__card--stack .specimen__card-text`
  paragraph (and its `.specimen__thread` line, if that also went stale) in the same PR.
- If step 5 found drift, fix the specific stale bullet(s) inside that repo's `.specimen__card--
  automation` card's `ul.specimen__list` (see the table above for its label id) — correct just
  what's now wrong, don't restructure or add to the list otherwise, and see step 5's note on the
  disclosure-toggle bullets before editing one that's hidden by default.

Each PR touches only its own repo's article — sections for different repos live in
non-overlapping parts of the same file, so independent PRs from independent branches merge
cleanly regardless of order.

### 7. Open the PR — never merge it

```bash
gh pr create --repo danibsheehan/danibsheehan.github.io --title "..." --body "..."
```

One PR per source repo with signal (skip repos with nothing to report — see step 2). Why-first PR
description: what changed in that source repo this week, in the same plain language as the blurb
itself. **Do not merge any of these PRs.** Merging is always a manual, separate decision — this
skill's job ends at opening them.

## Anti-patterns

- Editing anything in a source repo itself — this skill only reads those repos.
- Merging a portfolio PR, or leaving one in a state that looks pre-approved.
- Letting a "Recent updates" block accumulate more than the current week's entry.
- Leaving the `specimen__byline` date from a prior run in place — it must be replaced with this
  run's date every time, even if the blurb text itself barely changes.
- Rewriting the Pitch (step 4a) or Stack card (step 4b) for a routine week (dependency bumps,
  minor fixes) — save those rewrites for genuinely structural or tech-stack changes, respectively.
- Leaving an "Automation and AI" bullet in place after this week's change made it factually
  wrong (e.g. describing an auto-merge scope that no longer matches reality) — or, the opposite
  mistake, rewriting that section wholesale or adding a bullet for a one-off change that isn't a
  standing piece of automation.
- Changing a bullet's `hidden` attribute or letting the "Show N more" disclosure-toggle count
  drift out of sync with the actual number of hidden bullets while editing "Automation and AI".
- Inventing or embellishing changes that didn't happen.
- Naming a mechanism (a cache, a rate limit, a race condition) without translating *why it
  mattered* in the same breath — technical detail earns its place by serving the story, not by
  demonstrating it exists.
- Writing like a changelog bot: listicle openers, empty superlatives, hedging qualifiers, generic
  AI-shaped sentences that could describe any project's commit history.
- Opening a PR for a repo whose week had nothing people-relevant to report — *unless* the
  staleness threshold in step 2 has been crossed, in which case not opening one is the mistake.
- Blending signal from two source repos into one PR, or one repo's blurb leaking details from
  another repo's week.
- Dressing up a staleness-refresh PR (step 2's ~3-week fallback) as if real news happened, or
  running the staleness check on every single quiet week instead of only once the threshold is
  crossed.

## Monthly accuracy audit (Pitch / Stack / Automation and AI)

The weekly routine above (steps 4 and 5) can only ever catch drift that shows up in that week's
diff — a change that directly contradicts something already written. It structurally cannot
catch slow, cumulative drift (several small changes spread across many weeks, each individually
beneath the "structurally notable" or "directly contradicts a bullet" bar, that together make a
section wrong) or drift that predates a given check's existence. This pass exists to catch what
the weekly diff-based checks can't — it runs on a separate monthly cadence, independent of any
given week's signal.

For each source repo in the "Repos covered" table, independently:

1. Read that repo's current `.specimen__pitch`, `.specimen__card--stack .specimen__card-text`,
   and `.specimen__card--automation` sections on the live portfolio page (`projects/index.html`
   in `danibsheehan/danibsheehan.github.io`).
2. Read the actual current state of the source repo — not a diff, the live ground truth: its
   README/AGENTS.md description of what it does and how it's built, its CI/workflow config
   (Dependabot auto-merge scope, required checks, scheduled workflows), and any Claude Code /
   Cursor skills describing autonomous behavior.
3. Compare claim by claim: does every sentence in the Pitch and Stack text, and every bullet in
   "Automation and AI" (including the ones collapsed behind the disclosure toggle), still hold?
   Flag anything wrong, outdated, or describing automation that no longer exists (or exists but
   now works differently).
4. Nothing drifted for that repo → do nothing, no PR. Don't manufacture an audit finding just to
   have something to show for the pass.
5. Something drifted → open one PR for that repo (same one-repo-one-PR, branch-per-repo
   discipline as the weekly routine — never merge it) correcting only the specific stale
   claim(s)/bullet(s). Same rule as the weekly drift check: fix what's wrong, don't rewrite the
   whole section unless multiple things are simultaneously wrong.

This pass never touches "Recent updates" — that section, staleness fallback included, is the
weekly routine's responsibility (step 2 above).

**Setting up the monthly cadence**: this skill doesn't control how often it's invoked — a
separate scheduled Claude Code cloud routine does that, the same way the "Weekly portfolio
update" routine invokes the weekly steps above. A second routine (e.g. "Monthly portfolio
accuracy audit") needs to be created in
[claude.ai/code/routines](https://claude.ai/code/routines) (or via the `schedule` skill /
`RemoteTrigger`), on a monthly cadence, with a prompt pointing at this section specifically and
the same session sources (this repo plus all four source repos) as the weekly routine.

## Adding a new repo to coverage

1. Add a row to the "Repos covered" table above with the new repo, its portfolio section anchor,
   its `specimen--<slug>` modifier class, and its "Recent updates" / "Automation and AI" label
   ids (check `projects/index.html` in `danibsheehan/danibsheehan.github.io` for all of these).
2. Add the new repo to the **"Weekly portfolio update"** Claude Code cloud routine's session
   sources — its prompt now reads this table directly, but the cloud session can only check out
   repos explicitly attached to it. In [claude.ai/code/routines](https://claude.ai/code/routines)
   (or via the `schedule` skill / `RemoteTrigger update`), add
   `{"git_repository": {"url": "https://github.com/danibsheehan/<new-repo>"}}` to
   `job_config.ccr.session_context.sources`. Skipping this step means the routine still says
   nothing wrong in its prompt, but it still can't read the new repo.
3. Add a pointer in the new repo's own README/AGENTS.md, mirroring the ones in `caught-looking`,
   `musing`, `baseball-collection`, and `gotta-catch-em-all` (see this repo's README for the
   pattern), so an agent working there isn't left wondering why there's no local automation doc.

## Reference

- Target sections: `projects/index.html` in `danibsheehan/danibsheehan.github.io` — each project
  article's `.specimen__pitch`, `.specimen__card--stack`, `.specimen__card--updates`, and
  `.specimen__card--automation`. See the "Repos covered" table above for each source repo's
  anchor, modifier class, and label ids.
- Autonomy boundary: this repo's [`README.md`](../../../README.md#autonomy-boundary) — "opens,
  never merges" is the guardrail for this routine.
