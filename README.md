# napticstack-skills

Naptic-owned overlay skills layered on top of `garrytan/gstack` via the `napticstack-plugins` Claude Code marketplace.

## Compose-with-gstack contract

This repo provides **UNF (Unified Naptic Framework) wrappers around gstack skills**. Each wrapper adds Naptic-specific framing (org conventions, security posture, phase context) and then defers to the underlying gstack skill it wraps. Specifically:

- `naptic-pickup` wraps gstack `/picking-up-work` with UNF phase context (which engagement phase the session belongs to, how to pick the next task in that phase).
- `naptic-review` wraps gstack `/review` with NapticStacks org conventions (review-requested-to `maydaycyber`, per-org git identity rules, label/project-board hygiene).
- `naptic-cso` wraps gstack `/cso` with Naptic security posture (Bedrock Guardrails, Mayday/Naptic tenant separation, secrets-manager-first credential handling).
- `naptic-phase-04` is a UNF Phase 04 helper (planning / scoping bridge between client discovery and tenant-register).

The overlay is **thin** — wrappers add framing and call the gstack skill underneath; they do not reimplement gstack behavior.

**Prerequisite:** the `gstack` plugin must be installed first. The overlay assumes gstack skills are already present at `~/.claude/skills/<name>/` and will not function correctly without them.

## Status

Skeleton only as of 2026-04-30. Wrapper bodies are owned by `/home/jmay/.claude/plans/unf-phase-map-overlay-skills.md` (sibling plan) and will land in a follow-up PR. The current `SKILL.md.tmpl` files are intentionally inert stubs so the install scaffolding can ship and be exercised independently.

## Install

```
claude plugin install napticstack-skills@napticstack-plugins
```

This assumes the `napticstack-plugins` marketplace is already registered. See the bootstrap instructions in `NapticStacks/claude-plugins` for how to register it.

## What it modifies on disk

On install, the `setup` script symlinks each `skills/naptic-*/` into `~/.claude/skills/naptic-*/`. It does not touch gstack skills, the `~/.claude/skills/gstack/` directory, or any user-authored skills. If a non-symlink already exists at any target path, `setup` refuses to clobber it and prints a warning.

## Layout

```
skills/
  naptic-pickup/SKILL.md.tmpl
  naptic-review/SKILL.md.tmpl
  naptic-cso/SKILL.md.tmpl
  naptic-phase-04/SKILL.md.tmpl
UNF_PHASE_MAP.md
setup
.claude-plugin/plugin.json
hooks/hooks.json
```

## Authoring

Templates use the `.tmpl` extension while the overlay is in skeleton state. The sibling plan (`/home/jmay/.claude/plans/unf-phase-map-overlay-skills.md`) will rename them to `SKILL.md` once content is filled in. The `setup` script symlinks the parent skill directory itself, so the rename does not require any change to install scaffolding — symlinks resolve through to whichever filename the directory contains.
