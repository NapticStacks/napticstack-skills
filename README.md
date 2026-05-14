# napticstack-skills

Naptic-owned team skills distributed via the `napticstack-plugins` Claude Code marketplace. Two flavors live here:

1. **UNF wrappers** — thin overlays around `garrytan/gstack` skills that add Naptic framing.
2. **Standalone team skills** — first-party Naptic skills that are not wrappers (e.g. `sprint-recap`).

## UNF wrapper contract

UNF (Unified Naptic Framework) wrappers add Naptic-specific framing (org conventions, security posture, phase context) and then defer to the underlying gstack skill they wrap:

- `naptic-pickup` wraps gstack `/picking-up-work` with UNF phase context (which engagement phase the session belongs to, how to pick the next task in that phase).
- `naptic-review` wraps gstack `/review` with NapticStacks org conventions (review-requested-to `maydaycyber`, per-org git identity rules, label/project-board hygiene).
- `naptic-cso` wraps gstack `/cso` with Naptic security posture (Bedrock Guardrails, Mayday/Naptic tenant separation, secrets-manager-first credential handling).
- `naptic-phase-04` is a UNF Phase 04 helper (planning / scoping bridge between client discovery and tenant-register).

The overlay is **thin** — wrappers add framing and call the gstack skill underneath; they do not reimplement gstack behavior.

## Standalone team skills

- `sprint-recap` — generate a 3-paragraph plain-English recap (7th-grade reading level) of what shipped in a session. Designed to auto-fire via a Stop hook so non-technical teammates (Alex, Jenn, investors) can read the recap without parsing diffs. Promoted to the team plugin so every Naptic operator gets the same recap discipline.

**Prerequisite:** the `gstack` plugin must be installed first. The overlay assumes gstack skills are already present at `~/.claude/skills/<name>/` and will not function correctly without them.

## Status

UNF wrappers are still skeleton — wrapper bodies are owned by `/home/jmay/.claude/plans/unf-phase-map-overlay-skills.md` (sibling plan) and will land in a follow-up PR.

`sprint-recap` is the first non-skeleton skill in this repo and ships at 0.1.0 (2026-05-14).

## Install

```
claude plugin install napticstack-skills@napticstack-plugins
```

This assumes the `napticstack-plugins` marketplace is already registered. See the bootstrap instructions in `NapticStacks/claude-plugins` for how to register it.

## What it modifies on disk

On install, the `setup` script symlinks each `skills/<name>/` into `~/.claude/skills/<name>/`. It does not touch gstack skills, the `~/.claude/skills/gstack/` directory, or any user-authored skills. If a non-symlink already exists at any target path, `setup` refuses to clobber it and prints a warning.

If you previously installed `sprint-recap` manually at `~/.claude/skills/sprint-recap/` (as a regular directory, not a symlink), `setup` will warn and skip. Remove the local copy first if you want the plugin version to take over: `rm -rf ~/.claude/skills/sprint-recap`, then re-run `setup`.

## Layout

```
skills/
  naptic-pickup/SKILL.md
  naptic-review/SKILL.md
  naptic-cso/SKILL.md
  naptic-phase-04/SKILL.md
  sprint-recap/SKILL.md
UNF_PHASE_MAP.md
setup
.claude-plugin/plugin.json
hooks/hooks.json
```

## Authoring

UNF wrappers use the `.tmpl` extension while still in skeleton state. The sibling plan (`/home/jmay/.claude/plans/unf-phase-map-overlay-skills.md`) will rename them to `SKILL.md` once content is filled in. The `setup` script symlinks the parent skill directory itself, so the rename does not require any change to install scaffolding — symlinks resolve through to whichever filename the directory contains.

Standalone team skills (like `sprint-recap`) ship as `SKILL.md` directly — they are not wrappers and have no skeleton phase.
