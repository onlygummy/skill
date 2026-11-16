# onlygummy's AI Skills

A personal collection of AI agent skills (SKILL.md-based) authored by onlygummy.
Not affiliated with any organization.

## What is an Agent Skill

An Agent Skill is a folder containing a `SKILL.md` file plus optional
`references/`, `scripts/`, and `assets/`. The agent reads only the skill's
metadata at session start, then loads the full instructions when the task
matches, and pulls resources on demand. This is called progressive disclosure,
and it keeps context small.

## Skills included

| Skill | What it does |
| --- | --- |
| `skill-authoring` | Author, structure, and refine AI agent skills (SKILL.md + references/scripts/assets). |

## Install

Copy or symlink a skill folder into your harness's skills directory.

```sh
# OpenCode
cp -r .agents/skills/skill-authoring .opencode/skills/

# Claude Code
cp -r .agents/skills/skill-authoring .claude/skills/

# Codex CLI
cp -r .agents/skills/skill-authoring .agents/skills/
```

Symlinks work too, if your harness follows them. Place the skill under any
directory your harness scans (`.github/skills/`, `.gemini/skills/`, etc.).
See the skill's `SKILL.md` for harness-specific notes.

## Notes

- Skills are written in English for portability.
- Review `SKILL.md` and any `scripts/` before installing a skill from an
  untrusted source.

## Author

onlygummy

## License

MIT