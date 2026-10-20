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
| [software-design](skills/software-design) | Refactor, review, and structure code with SOLID, the OOP pillars, DRY/KISS/YAGNI, the 23 GoF patterns, and a smell-to-refactoring catalog, biased toward the smallest change that removes a concrete pain. Pairs with the [mcp-software-design](https://github.com/qwertymuzaffar/mcp-software-design) MCP server for smell heuristics and pattern scaffolding. |

Each skill works on its own. When its companion MCP server is connected, the
skill uses the server's tools for the parts that can be checked
deterministically and keeps the judgment calls in the skill text.

## Layout

Each skill lives in `skills/<name>/` with a `SKILL.md` and optional
`references/`, `scripts/` and `assets/` folders beside it. There is no
`SKILL.md` at the repository root, so the CLI discovers every skill under
`skills/`.

## License

MIT
