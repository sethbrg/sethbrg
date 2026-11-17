# Research team

This repo holds a three-agent research team run from Claude Code. Full design: `docs/BLUEPRINT.md`.

- Run it with `/brief <question>`. The pipeline is defined in `.claude/skills/brief/SKILL.md`.
- Agents: `.claude/agents/researcher.md`, `writer.md`, `reviewer.md`. Each agent writes one file only.
- Outputs go in `briefs/<slug>/` (`evidence.md`, `brief.md`, `review.md`). Every run is logged in `briefs/log.md`.

## Rules
- Ask the user before committing, pushing, publishing, sending, deleting, or spending money.
- Never ask for or store passwords, API keys, or other secrets in chat or in files.
- This repo is a GitHub profile repo. Treat everything committed here as potentially public.
- Facts in a brief must trace to an entry in that brief's `evidence.md`.
