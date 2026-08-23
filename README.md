# abc-skills

Single source of truth (SSOT) for the ABC LEGACY UX audit skill family.
Agent Skills format — the same SKILL.md runs on claude.ai, Claude Code and the API.

| Skill | Dimension |
|---|---|
| ux-psychology-audit | words & flows (P1-P6, Part C, Part T) |
| ux-layout-composition-audit | geometry (S1-S5, Part R) |
| ux-design-system-audit | token compliance (Part V) |
| ux-component-sourcing | how to obtain components (Build ladder) |
| ux-interaction-performance-audit | time / main thread (M1-M9, Part O) — v1.4 |

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

- `./validate.sh` must pass before commit (description <= 1024 bytes, name
  format, folder == name). Descriptions are single-line.
- Method layer only in skills; project values (budgets, tokens) live in each
  project's CLAUDE.md.
- Add-only edits, shown as diffs; every patch cites source evidence (field
  runs or taught material). One mechanism = one finding.

## abc-lab/ — the family's kept-tooling workspace

Skills never write into an audited project's tree. Measurements and rigs are
disposable by default. When a project owner explicitly asks to KEEP a
harness/rig/script a skill created, it lives at
`abc-lab/skills/<skill-name>/<harness>/` in the project root — namespaced by
owning skill, runtime files git-ignored, a one-line README inside. Contract:
`rm -rf abc-lab` removes every trace of the family's tooling from the
project. (Convention defined in ux-interaction-performance-audit v1.4;
applies to the whole family.)

## Migration notes — 2026-08-21 seed

- layout & design-system descriptions trimmed to fit the 1024-byte platform
  limit (routing content unchanged): "in code conditionals" -> "in
  conditionals", "cannot" -> "can't", "or when auditing" -> "or auditing";
  design-system sibling parenthetical shortened.
- ux-interaction-performance-audit seeded at v1.3 (after two live field runs).
- SSOT stamp line appended to every SKILL.md; from here, git history is the
  version record.
