# abc-skills

Single source of truth (SSOT) for the ABC LEGACY UX audit skill family.
Agent Skills format — the same SKILL.md runs on claude.ai, Claude Code and the API.

| Skill | Method |
|---|---|
| ux-audit-psychology | words & flows (P1-P6, Part C, Part T) |
| ux-audit-layout-composition | geometry (S1-S5, Part R) |
| ux-audit-design-system | token compliance (Part V) |
| ux-sourcing-component | how to obtain components (Build ladder) |
| ux-audit-interaction-performance | time / main thread (M1-M9, Part O, tools/measure.mjs) |

## Flow

edit -> `./validate.sh` -> commit & push -> **server:** `git pull` (Claude Code
reads live) -> **claude.ai:** re-upload the changed skill via the Save/Update
dialog (manual deploy — no API exists for the account skill library).
claude.ai auditor sessions fetch this repo read-only to verify freshness.

## Server install (Claude Code, machine-wide)

```bash
git clone https://github.com/AlonurKomilov/skills.git ~/abc-skills
mkdir -p ~/.claude/skills
for d in ~/abc-skills/ux-*/; do ln -s "$d" ~/.claude/skills/$(basename "$d"); done
```

Claude Code follows symlinks; project-specific skills stay in each repo's
`.claude/skills/`. Update everywhere with `git -C ~/abc-skills pull`.

## Rules

- `./validate.sh` must pass before commit: strict-YAML frontmatter,
  description <= 1024 bytes (single line, YAML-safe — no `: ` colon+space
  inside plain scalars), name format, folder == name, name == taxonomy.
- Method layer only in skills; project values (budgets, tokens) live in each
  project's CLAUDE.md.
- Add-only edits, shown as diffs; every patch cites source evidence (field
  runs or taught material). One mechanism = one finding.

## Taxonomy

Every skill answers four questions in its frontmatter, in this order:

| Field | Question | Current values |
|---|---|---|
| `family` | Who made this? | `abc` (constant across the whole repo) |
| `domain` | Where does it apply? | `ux` today; `backend`, `infra`, … as the repo grows |
| `kind` | What does it DO? | `audit` \| `sourcing` |
| `method` | HOW — which dimension/technique? | `psychology` \| `layout-composition` \| `design-system` \| `interaction-performance` \| `component` |

Frontmatter fields are written in this exact order — `family`, `domain`,
`kind`, `method` — so reading the card top to bottom spells the name:
domain + kind + method. (`name` itself isn't shown as a labeled field on
claude.ai — the platform already surfaces it as the skill's title.)

**Naming invariant:** `name == {domain}-{kind}-{method}` — e.g.
`ux-audit-psychology`, `ux-sourcing-component`. `./validate.sh` enforces
this mechanically; a frontmatter that disagrees with the folder/name fails
review. A future backend skill slots in the same way, e.g.
`backend-audit-query-performance`, with no change to this scheme.

## Which skill when

| Symptom / question | Skill |
|---|---|
| Wording or flow is confusing; motivation feels off; onboarding stalls | `ux-audit-psychology` |
| Layout "looks mixed"; can't tell where a section ends; drag targets unclear | `ux-audit-layout-composition` |
| Hardcoded colors/spacing/fonts; a value that isn't from the design system | `ux-audit-design-system` |
| Surface freezes/lags/stutters; "is it my PC or the code?" | `ux-audit-interaction-performance` |
| Need a UI capability — reuse, compose, build, or install? | `ux-sourcing-component` |

## Versioning

One version per skill folder, semver `vMAJOR.MINOR.PATCH`:

- **MAJOR** = the report contract (the "Framework version" in the skill's
  output format) — bumps only on breaking changes to statuses/format that
  affect cross-session rollups.
- **MINOR** = new capability: a new Part, mechanism class, pattern (e.g.
  seeded-rig), or bundled tool.
- **PATCH** = fixes, clarifications, and metadata (e.g. taxonomy fields)
  that add no capability.

One number, three places, always in the same commit as the change:

1. the frontmatter `version:` field — renders as a table row on GitHub and
   as a labeled field in the claude.ai preview; `family`/`domain`/`kind`/
   `method`/`source` sit alongside it. The last line of every SKILL.md
   stays a version-less SSOT pointer:
   `<!-- SSOT: github.com/AlonurKomilov/skills -->`.
2. the commit message prefix: `<name> vX.Y.Z: …`
3. the claude.ai deploy — upload only from this repo, so a library copy
   always answers "which version am I?" in its own frontmatter.

`./validate.sh` extracts and prints every skill's version + taxonomy and
rejects non-strict-YAML frontmatter or a name/taxonomy mismatch; a content
change without a version bump fails review. Git log is the changelog — no
separate CHANGELOG file. Sync check = compare frontmatter versions
(library vs repo HEAD) first, then hashes when versions agree.

Baselines (2026-08-24): psychology **v2.0.3** · layout **v1.0.3** ·
design-system **v1.0.3** · sourcing **v1.0.3** ·
interaction-performance **v1.5.3**.

## abc-lab/ — the family's kept-tooling workspace

Skills never write into an audited project's tree. Measurements and rigs are
disposable by default. When a project owner explicitly asks to KEEP a
harness/rig/script a skill created, it lives at
`abc-lab/skills/<skill-name>/<harness>/` in the project root — namespaced by
owning skill, runtime files git-ignored, a one-line README inside. Contract:
`rm -rf abc-lab` removes every trace of the family's tooling from the
project. Kept harnesses carry no credentials in their tracked code — auth
material lives only in runtime-written, gitignored files, backed by a
root-level `.gitignore` belt across the whole lab. (Convention defined in
ux-audit-interaction-performance v1.4–v1.5; applies to the whole family.)

## Migration notes — 2026-08-21 seed

- layout & design-system descriptions trimmed to fit the 1024-byte platform
  limit (routing content unchanged): "in code conditionals" -> "in
  conditionals", "cannot" -> "can't", "or when auditing" -> "or auditing";
  design-system sibling parenthetical shortened.
- ux-interaction-performance-audit seeded at v1.3 (after two live field runs).
- 2026-08-23: versions moved to frontmatter (`version:` + `source:` keys);
  bottom stamps reduced to the version-less SSOT pointer; perf description
  made strict-YAML-safe ("Reads TIME:" colon -> em-dash).
- 2026-08-23: taxonomy fields (`family`/`domain`/`kind`/`method`) added to
  every frontmatter; validate.sh v4 enforces the name-invariant.
- 2026-08-23: frontmatter field order corrected to `family, domain, method,
  kind` (was `family, domain, kind, method`) so the card read top-to-bottom
  in the same order the name was built at the time.
- 2026-08-24: renamed all five skills to {domain}-{kind}-{method}
  (ux-psychology-audit -> ux-audit-psychology, ux-layout-composition-audit ->
  ux-audit-layout-composition, ux-design-system-audit -> ux-audit-design-system,
  ux-interaction-performance-audit -> ux-audit-interaction-performance,
  ux-component-sourcing -> ux-sourcing-component). Folders, frontmatter
  `name:`, all cross-references, and validate.sh's invariant updated together;
  frontmatter field order flipped back to `family, domain, kind, method` to
  match.
