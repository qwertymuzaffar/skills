# skills

Agent skills by [@qwertymuzaffar](https://github.com/qwertymuzaffar), in the
[Agent Skills](https://agentskills.io) format. They install into Claude Code,
Cursor, Codex, OpenCode and the other agents supported by the
[skills CLI](https://github.com/vercel-labs/skills).

## Install

```bash
# pick skills interactively
npx skills add qwertymuzaffar/skills

# one skill, globally, into Claude Code
npx skills add qwertymuzaffar/skills --skill udacity-commit -g -a claude-code
```

## Skills

| skill | what it does |
| --- | --- |
| [udacity-commit](skills/udacity-commit) | Commit messages and branch names in the [Udacity Git Commit Message Style Guide](https://udacity.github.io/git-styleguide/) format, with a branch-first workflow. Pairs with the [mcp-udacity-commit](https://github.com/qwertymuzaffar/mcp-udacity-commit) MCP server for deterministic validation. |

## Layout

Each skill lives in `skills/<name>/` with a `SKILL.md` and optional
`references/`, `scripts/` and `assets/` folders beside it. There is no
`SKILL.md` at the repository root, so the CLI discovers every skill under
`skills/`.

## License

MIT
