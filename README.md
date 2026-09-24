# agentic-setup

Harness-agnostic personal setup for agentic coding: standing guidelines and
skills that work with any coding agent (ZCode, Codex, Claude Code, Cursor,
opencode, ...). No harness-specific config, hooks, or settings live here —
just portable markdown.

## Layout

```
agentic-setup/
├── AGENTS.md                     # standing personal guidelines
├── skills/                       # general skills, any project
│   ├── torvalds-doctrine/        # data-first coding doctrine
│   ├── rat-principle/            # fix the class, not the instance
│   ├── deslop/                   # cleanup conductor: baseline, then organize and reduce
│   ├── deslop-organize/          # every file one home: layout, docs, module seams
│   ├── deslop-reduce/            # delete code, prove the same result
│   └── tao-method/               # hard problems: playful, rigorous, stunningly simple
└── projects/
    └── lineups/                  # Lineups-stack skills that extend the general ones
```

Project skills in `projects/<project>/skills/` extend a general skill and
name their project in the description, so they trigger only there. Install
them next to the general skills they extend.

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
- `skills/deslop-organize` borrows its module vocabulary (depth, seam,
  locality, the deletion test) from Matt Pocock's
  [codebase-design](https://github.com/mattpocock/skills/tree/main/skills/engineering/codebase-design)
  and
  [improve-codebase-architecture](https://github.com/mattpocock/skills/tree/main/skills/engineering/improve-codebase-architecture)
  skills, which build on John Ousterhout's *A Philosophy of Software Design*.
- The deslop skills also draw on Kent Beck's rules of simple design, Martin
  Fowler's refactoring catalog, Michael Feathers' characterization tests,
  Knuth and Lamport on code as explanation, and Linux kernel documentation
  practice.
- `skills/tao-method` distills Terence Tao's
  [career advice](https://terrytao.wordpress.com/career-advice/) and
  problem-solving posts.

## License

[MIT](LICENSE)
