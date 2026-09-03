---
name: ux-audit-disclosure-depth
version: 1.1.0
family: abc
domain: ux
kind: audit
method: disclosure
scope: depth
source: https://github.com/AlonurKomilov/skills
description: Disclosure-depth audit — sixth member of the ux audit family (psychology=words/flows, layout=geometry, design-system=tokens, performance=time, sourcing=components). Reads DEPTH — which rendered values are questions in disguise. For every datum on a surface it scores six evidence-backed factors (depth beneath, decision weight, question frequency, volatility, cross-links, cost) and returns INTERACT / CANDIDATE / SERVED / FLAT / OVER-DISCLOSED — with the hidden question, today's workaround and the LOWEST depth rung that answers it. Classifies values by CLASS (level, counter, status, identifier, timestamp, reference, money, text), never by domain noun. Use when a detail or dashboard page "feels flat", when deciding what should be clickable, hoverable or drill-in, when a reference product is "layered" and yours is not, after adding a data-rich surface, or when the user asks "what here deserves interaction". Report IN CHAT (no files by default).
---

# UX Audit — disclosure depth

The siblings read words and flows, geometry, tokens and time. This
skill reads **depth**: what lies beneath each rendered value, and
whether the surface offers a path to it. A page can be clearly worded,
well composed, on-token and fast — and still leave the user doing
mental arithmetic, scrolling for a chart, or opening another page to
answer the question a single number just raised. That number is a
**question in disguise**. This skill finds those, and — just as
importantly — finds interactions that exist without a question behind
them.

> **Universality rule (read first).** This skill knows NO domain
> nouns. It classifies every value by CLASS and scores it by EVIDENCE
> found on the surface, in the code and in the data source. Every
> concrete example in this file is an illustration of a class, never a
> thing to search for: an auditor who greps a project for the words in
> this file has misread it. If a project noun ever appears in this
> skill's text, that is a bug in the skill.

> **Boundary — six audits, one question each (route, never
> double-report):**
>
> | The finding sounds like… | It belongs to… |
> |---|---|
> | "should this value open into anything at all, and how deep" · "this control opens nothing worth opening" | **this skill** |
> | "which control should the interaction be" (popover vs drawer vs page) once depth is decided · "sibling values disagree on treatment" · "the copy inside the popover is unclear" | `ux-audit-psychology` (T2 / T5 / C1–C3) |
> | "the affordance isn't visible before hover" · "the drill-in target is too small" · "the expanded state shifts the layout" | `ux-audit-composition-layout` (S3 / S5 / S4) |
> | "the hint colour / icon step isn't from the system" | `ux-audit-compliance-design-system` |
> | "the popover opens slowly" · "the sparkline janks" | `ux-audit-performance-interaction` |
> | HOW to obtain the popover / chart / drawer | `ux-sourcing-component` (this skill only tags `Build`) |

## Scope modes

Same A/B/C/D as the family — pick exactly one; the working tree is
truth, git only locates scope.

- **Mode A — Session:** the surfaces built or changed this session.
- **Mode B — Project:** walk data-rich surfaces first — detail pages,
  dashboards, cards with many key-value pairs, tables with numeric
  columns — then the rest; end with "Not yet audited".
- **Mode C — Targeted:** exactly the surface or values the user names.
- **Mode D — Live UI:** screenshots or a browser. Depth is judged from
  what is RENDERED (the value, its hint, its current affordance) PLUS
  what the source proves exists beneath it — so Mode D never replaces
  the code/API read; it joins it (`[code+ui]`). Never commit state
  while auditing; no destructive or irreversible actions.

Rendered-only context (a screenshot, no repo): D1 and D5 cannot be
proved — score them `?`, report totals as ranges, and say plainly that
every verdict is a hypothesis until the data source is read. A live
page whose own network payloads and shipped bundles are readable is
NOT rendered-only: the fields those responses actually return are
source evidence for D1 and D5, and are cited as such.

## Step 0 — Grounding (mandatory, never skip)

**0a. Data reality.** For the surface in scope, read where its values
come from — API response shape, model/schema, integration payloads,
existing history/series endpoints. The question "is there more beneath
this value?" is answered by EVIDENCE, never assumed: a value has depth
only when the source can supply history, a breakdown, provenance, or a
related object. Note what exists, what is derivable, and what does not
exist at all.

**0b. The project's depth vocabulary.** Inventory the interaction
primitives the project already ships for revealing more: tooltips,
popovers, expandable rows, drawers, detail routes, charts. The lowest
rung recommended later must be phrased in THIS vocabulary.

**0c. Values layer.** Read CLAUDE.md / design docs for disclosure rules
(what may be a tooltip, what must be a page, hint conventions). Rules
found → audit against them. None found → `SSOT-GAP` with a proposed
rule; the thresholds below are the universal floor, not a project's
law.

## Step 1 — Datum census (collect everything before judging anything)

One row per rendered value on the surface. A "datum" is any single
piece of information a user reads: a number, a label with a value, a
status, a code, a time, a link, a short text. Group repeated values
(a column, a list of identical cards) as one row with a count.

Columns:

- **Class** — exactly one of:
  `level` (a measure that moves and has a range — a percentage, a
  gauge, a rate), `counter` (accumulates monotonically — a total, a
  lifetime tally, a running count), `status` (a member of a
  small enum — on/off, open/closed, tiers), `identifier` (an opaque
  code — ID, serial, plate, hash), `timestamp` (a moment or duration),
  `reference` (names another object — an address, a person, a source
  system, a parent record), `money` (a currency amount), `text`
  (free prose).
  Class is decided by what the value IS, not by its label: a label
  reading "score" attached to a monotonically growing number is a
  `counter`.
- **Current affordance** — `flat` (static text), `hint` (a colour,
  dot, icon, underline or badge signals something more but offers NO
  path), `interactive` (ANY path — tooltip, popover, expand, link,
  chart hover — name what it opens). A tooltip is a path, however
  small: it is `interactive`, never `hint`.
- **Answers** — for an `interactive` row, the ONE question its existing
  path answers, in a phrase ("how fresh is this reading", "where on
  the map"). `—` for `flat`/`hint`. This column is compared with the
  hidden question in Step 4: a path that answers a different question
  is a wrong-question disclosure, not an answer.
- **Evidence** — quote the rendered value and the element/props/class
  that give it its affordance; screenshots count as `[ui]`, source as
  `[code]`.

The census is the audit's completeness proof: a value not in it was
silently skipped. Every `hint` row is a lead — a hint is the surface
half-admitting a question it does not answer.

## Step 2 — Score D1–D6 (0–2 each, evidence per factor, 12 max)

Score every census row. A factor without evidence is `?`, and the
total becomes a RANGE (min counts `?` as 0, max as 2). Never print a
single number the evidence does not support.

| # | Factor | 0 | 1 | 2 |
|---|---|---|---|---|
| D1 | **Depth beneath** — does the source hold more behind this value (history, breakdown, provenance, related record)? | nothing beyond the value itself | derivable but not stored, or one extra fact | stored history / breakdown / provenance ready to show |
| D2 | **Decision weight** — does a user act on this value? | read for orientation only | occasionally informs an action | a recurring decision hinges on it (the product already reacts — alert, filter, badge — to this value) |
| D3 | **Question frequency** — how often does "why / since when / how much / what next" arise here? | practically never | at edge values or on exception | routinely, on ordinary visits |
| D4 | **Volatility** — does the value change so that its history means something? | fixed for the object's life | changes on events | changes continuously or per session |
| D5 | **Cross-links** — does it lead to other objects the user needs next? | none | one related object | several, or a workflow continues there |
| D6 | **Cost & safety (inverted)** — how cheap and safe is the lowest rung that answers the question? | needs a new page, new data, or a write-path | needs a new query or new component | read-only, data already loaded, project primitive exists |

Evidence discipline per factor:
- D1 and D5 cite the SOURCE (endpoint, field, model) — never the label.
- D2 cites product behaviour (an alert, a filter chip, a threshold in
  code) or the user's stated workflow. A judgment call is written as
  `?`, never as a cautious `1` — a conservative number still prints as
  a number, and the range mechanism never fires.
- D3 is `?` BY DEFAULT. It earns a number only from a cited
  observation — the user's own statement, analytics, support tickets,
  a recorded session. The product reacting to a value (alert, badge,
  filter) is D2 evidence, not D3. Expect most rows of a first run to
  carry a D3 `?` and therefore a range; that is the method working.
- D4 and D6 are usually provable from code alone.

**Class priors (starting expectations, not verdicts):** `level` and
`money` tend high on D4/D2; `counter` middling (D4 yes, D3 rarely);
`status` hinges on D2 (a status the product reacts to is a question,
a decorative one is not); `identifier` almost never drills in — its
need is a UTILITY action (copy, open-in-source), which is a psychology
T2 matter, not depth; `timestamp` earns depth only when a sequence
exists behind it; `reference` earns it when the referenced object has
its own surface; `text` rarely. Priors are overridden by evidence
every time.

## Step 3 — Verdicts

| Status | Meaning |
|---|---|
| `INTERACT` | ≥ 9/12 (75%) — a hidden question exists and the surface offers no path to it, OR the only path answers a different question (a freshness tooltip on a value whose question is its trend) — say which |
| `CANDIDATE` | 6–8/12 (50–74%) — worth it in some products; the values layer or the user decides |
| `SERVED` | ≥ 6/12, but the hidden question is already answered within one glance on the SAME surface — an adjacent chart, a sibling value, the content of the existing path. Name where. Not a finding; census only |
| `FLAT` | ≤ 5/12 — static is correct; say which factors keep it there |
| `OVER-DISCLOSED` | currently `interactive` yet ≤ 5/12 — an interaction with no question behind it; propose removal or demotion. A value rendered twice on one surface is NOT this status — it is a sibling-consistency matter for `ux-audit-psychology` T5 |
| `DARK-PATTERN-RISK` | disclosure used to bury cost, consequence or a decline path behind an interaction — flag + the honest alternative |
| `NEEDS-CONTEXT` | a deciding factor is `?` — name the source, screen or user fact needed |

Thresholds are the universal floor; a project's values layer may
tighten them (say so in the report). A range that straddles a
threshold reports BOTH statuses ("CANDIDATE–INTERACT") until the `?`
is resolved. `SERVED` outranks the score: a high total with an
in-glance answer on the surface is served, and the report says by
what — the score then argues only for keeping that answer, not for
adding a path.

`OVER-DISCLOSED` is not optional. A skill that only adds interaction
is an interaction-inflation machine; half of this audit's value is
naming the clickable things nobody needed to click.

## Step 4 — For every `INTERACT` (and each `CANDIDATE` you argue for)

Three lines, then the rung:

1. **Hidden question** — what the user asks on seeing the value,
   in their words ("is this enough to finish today?", "when did this
   last change?", "what makes up this total?").
2. **Today's workaround** — how they answer it now: mental
   arithmetic, scrolling to a chart, opening another page, asking a
   colleague. Name it concretely; this IS the cost of staying flat.
3. **What changes** — the one sentence the user gets instead.

**Depth ladder — recommend the LOWEST rung that answers the hidden
question; never a higher one for spectacle:**

| Rung | Form | When |
|---|---|---|
| R0 | **Inline enrichment** — a second value beside the first (a derived figure, a delta, an "as of") | one fact answers the question |
| R1 | **Hover / tap disclosure** — tooltip or popover with a few facts or a small series | a glance answers it, no navigation |
| R2 | **Expand in place** — row/card opens a detail band | the answer is a short list |
| R3 | **Drawer / side panel** — keeps the surface context | the answer is a full record, context still needed |
| R4 | **Dedicated surface** — a page or route | the answer is a workflow or a large dataset |

Name the rung in the project's own vocabulary (Step 0b). The finding
carries `Impact · Effort · Build`; `Build: new-component` /
`new-dependency` hands off to `ux-sourcing-component`. WHICH control
implements the rung (popover vs toggletip vs card) is `ux-audit-psychology`
T2's call — this skill fixes the depth, not the widget.

## Step 5 — Ethics gate (mandatory, family standard)

Disclosure cuts both ways: it declutters, and it hides. For every
`INTERACT` proposal and every existing interaction ask: would we be
comfortable explaining to the user's face WHY this is behind a click?
A price, a consequence, a loss, or a way out that lives only behind
an interaction is `DARK-PATTERN-RISK`, however clean the surface
looks. Simplifying by hiding is not simplifying. The mirror case passes
through the same gate: absent data rendered as a favourable value — a
cost that could not be computed shown as zero — is omission dressed as
a measurement; the honest form is the empty state.

## Step 6 — Output format (mergeable with the family)

```markdown
# Disclosure-Depth Audit Report
- Framework version: 1
- Scope mode: <A/B/C/D, can combine> — <one line: what was reviewed>
- Date: <date> | Auditor session: <short id>
- Step 0 digest: data source <where> · depth vocabulary <primitives found> · values rules <found / SSOT-GAP>
- Surfaces audited: <n> | Not yet audited: <list or "none">

## Datum census
| # | Value (rendered) | Class | Affordance | Answers | D1 | D2 | D3 | D4 | D5 | D6 | Total | Status |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | <quoted> | level | interactive (tooltip) | how fresh | 2 | 2 | ? | 2 | 1 | 2 | 9–11 | INTERACT (wrong-question path) |
| 2 | <quoted> | counter | flat | — | 2 | 1 | ? | 2 | 1 | 2 | 8–10 | SERVED — chart below plots it |

## Findings
### <Surface name>
- **[Dn… — STATUS]** `[code|ui|code+ui]` <value> — hidden question: <…> · workaround today: <…> · change: <…> · rung: <R0–R4 in project vocabulary> `Impact: high|med|low · Effort: S|M|L · Build: existing|compose|new-component|new-dependency`
- **[OVER-DISCLOSED]** <value> — opens <what>; no question behind it (<factors>) · propose: <demote/remove>

## Routed to siblings
- <finding + destination>

## Depth rules proposed (candidates for the project's values layer)
- <rule stated for a CLASS, never for a named value>

## Top actions (highest impact first)
1. ...

## NEEDS-CONTEXT items
- <which factor of which value, and what would resolve it>
```

Rules: every score cites evidence or is `?`; totals with `?` print
as ranges; every `SERVED` names where on the surface the answer is; every `INTERACT` carries all three lines and a rung; every
`OVER-DISCLOSED` names the factors that fail; proposed rules are
written for classes ("a `level` the product alerts on gets at least
R1"), never for named values; findings that belong to siblings go
under "Routed", never scored here; the `Build` tag lives in the report
only; report in the user's language on request, statuses, class names
and factor ids stay English so rollups merge.

## Deliver IN CHAT (markdown, never a report file)

Post the full report in the chat reply. No report files, no invented
reports directory. If the user explicitly asks for a file, write
exactly ONE at the path THEY name. Durable outputs have real homes:
depth rules graduate into the project's design docs, `INTERACT` items
become tracker issues; the report stays ephemeral.

## Aggregation

Shares the family's statuses and `Impact · Effort · Build` tags on
purpose: rollups may merge with P/C/S/D/M findings into one ranked
list; the newest report per surface supersedes older ones; `Build:
new-*` items are verified (and downgraded when over-classified) by
`ux-sourcing-component`; reports from past sessions count only when
the user provides them — say so in the rollup header.

## Auditor anti-patterns

- Searching the project for the words in this file — audit CLASSES
  and EVIDENCE, never nouns.
- Printing a precise score over an unproven factor — `?` and a range;
  a "conservative 1" is the same mistake wearing modesty.
- Classifying a tooltip as `hint` — any path is `interactive`.
- Scoring a value INTERACT while the adjacent chart already plots it —
  that is `SERVED`; the footnote you were about to write is the status.
- Assuming depth exists because a value "sounds important" — D1 is
  read from the source or scored `?`.
- Recommending a higher rung than the hidden question needs.
- Reporting only additions — an audit with zero `OVER-DISCLOSED`
  candidates on an interaction-rich surface has not looked.
- Deciding the widget — that is psychology T2; deciding the library —
  that is sourcing.
- Proposing a depth rule for a named value instead of its class.

<!-- SSOT: github.com/AlonurKomilov/skills -->
