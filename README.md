# agentic-setup

Harness-agnostic personal setup for agentic coding: standing guidelines and
skills that work with any coding agent (ZCode, Codex, Claude Code, Cursor,
opencode, ...). No harness-specific config, hooks, or settings live here —
just portable markdown.

## Layout

```
agentic-setup/
├── AGENTS.md                     # standing personal guidelines
└── skills/
    └── torvalds-doctrine/        # engineering doctrine skill (SKILL.md)
```

## Using the guidelines

`AGENTS.md` follows the [agents.md](https://agents.md) convention that most
harnesses read automatically. Pick the scope you want:

- **Everywhere:** symlink or copy it to your harness's global instructions
  file (`~/.codex/AGENTS.md`, `~/.zcode/AGENTS.md`, or the equivalent).
- **One project:** append or include it below the project's own
  `AGENTS.md`, keeping project instructions in charge.

```bash
ln -s ~/agentic-setup/AGENTS.md ~/.codex/AGENTS.md
```

## Using the skills

Skills use the open Agent Skills format: a directory containing a
`SKILL.md` with `name` + `description` frontmatter. Harnesses that support
the format discover them from well-known locations:

- ZCode: `~/.agents/skills/<name>/` (user) or `.agents/skills/<name>/` (project)
- Claude Code: `~/.claude/skills/<name>/` or `.claude/skills/<name>/`
- Others: point the harness at `skills/`, or copy the directory in.

```bash
ln -s ~/agentic-setup/skills/torvalds-doctrine ~/.agents/skills/torvalds-doctrine
```

## Adding a skill

1. `mkdir skills/<kebab-case-name>`
2. Write `skills/<name>/SKILL.md` with `name` and `description` frontmatter.
   The description is the trigger signal — say what it does *and* when to
   use it, a little pushy.
3. Keep the body under ~500 lines; push detail into `references/` files the
   model reads on demand.

Test a new skill against at least two real prompts before keeping it.

## Credits

- `skills/torvalds-doctrine` adapted from
  [leopiney/linus-torvalds-skills](https://github.com/leopiney/linus-torvalds-skills)
  (MIT).
- `AGENTS.md` pruned from my per-project instruction files.

## License

[MIT](LICENSE)
