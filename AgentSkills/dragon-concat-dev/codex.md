# Codex Adapter: dragon-concat-dev

Use this adapter when running Codex on Dragon Concat / 左林右李 tasks.

## Canonical skill

Before editing or reviewing the Dragon Concat codebase, read the Hermes-compatible skill in this same directory:

`/home/hancheng/Documents/ObsidianThoughts/AgentSkills/dragon-concat-dev/SKILL.md`

That file is the source of truth for:

- repo shape and active implementation areas
- preferred local dev lifecycle commands
- backend/frontend test workflow
- PostgreSQL / Alembic expectations
- production deploy notes
- common scope-control pitfalls

## Codex usage pattern

When launching Codex manually, include an instruction like:

```text
First read /home/hancheng/Documents/ObsidianThoughts/AgentSkills/dragon-concat-dev/SKILL.md and follow it. Then work in /home/hancheng/project_dragon_concat.
```

If using a repo-level `AGENTS.md`, keep it as a lightweight pointer to this adapter and `SKILL.md`; do not duplicate the full skill unless cross-machine portability requires it.

## Important Codex-specific reminders

- Run Codex from inside the git repo when it needs to edit code.
- Prefer small, scoped changes; do not expand simple asset/content replacement tasks into redesigns.
- Use the project dev helper (`scripts/dev-stack.sh`) rather than manually spawning frontend/backend processes unless the task explicitly requires otherwise.
- For UI-visible changes, include browser/manual verification when possible.
- Treat `example/` as historical reference only, not the active implementation source.
