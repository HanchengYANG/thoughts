# dragon-concat-dev Agent Skill

This directory is the canonical shared skill store for Dragon Concat related agent guidance.

- `SKILL.md` is the Hermes-compatible entry point and keeps the YAML frontmatter Hermes needs.
- `codex.md` is the Codex adapter: point Codex prompts or repo-level `AGENTS.md` files here when Codex needs this skill.
- Future agent adapters can be added as separate files without duplicating the core skill content.

Canonical location:
`/home/hancheng/Documents/ObsidianThoughts/AgentSkills/dragon-concat-dev/`

Hermes location:
`/home/hancheng/.hermes/skills/software-development/dragon-concat-dev` symlinks to this directory.

Do not store secrets here. This vault may be backed up/synced through Git.
