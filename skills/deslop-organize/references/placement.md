# Where does this file go?

Decide by the file's reader, not by its extension. Before any move or
delete, grep for the full path, the basename, and the stem across code,
scripts, CI config, build manifests, and docs.

| The file is... | Destination |
|---|---|
| Project intro, build, and run steps | Root `README` — exactly one |
| License text | Root `LICENSE` |
| Agent behavior rules | Root `AGENTS.md` / `CLAUDE.md` — one each, no dated copies |
| How one module works or is used | `<module>/README.md`, next to the code |
| How a system or topic works | One maintained file under `docs/`, linked from the doc index |
| One architecture decision and why | `docs/adr/NNNN-title.md` (context, decision, consequences) |
| A known limitation or blocker list | The one file the project uses for that, or a section of the topic doc |
| Session handoff, resume note, status report, plan copy | Merge live facts into the project's single state file, if it has one; otherwise an out-of-tree notes directory. Never a new root file |
| Config or data a program reads | The directory that owns that data kind (`config/`, `data/`), next to its schema |
| JSON, CSV, or log output a run produced | Delete; gitignore the output path |
| Test fixture or golden file | The owning test directory (`testdata/`, `Tests/Fixtures/`, ecosystem equivalent) |
| Screenshot a maintained doc cites | An assets folder beside that doc |
| Screenshot or capture nobody cites | Out of tree (CI artifact store or an ignored evidence directory) |
| Build output, cache, coverage | Ignored directory; never tracked |
| One-off debug script | Delete, or an ignored `scratch/` directory |
| Script a gate, CI job, or doc calls | `scripts/` (automation) or `tools/` (built utilities), one purpose per file |
| Near-duplicate script | Merge into one with a parameter; delete the rest |
| `*_old`, `*_v2`, `*.bak`, `copy of *` | Delete — git history is the backup |
| Vendored third-party code | One `third_party/` or `vendor/` directory, with its license |

## Root allowlist template

```
ALLOWED at root
  README, LICENSE, CONTRIBUTING, SECURITY, CODE_OF_CONDUCT (one each)
  AGENTS.md, CLAUDE.md (one each)
  build and package manifests; the project descriptor the toolchain requires
  tool dotfiles; CODEOWNERS
  top-level directories, each with one purpose

DENIED at root
  any other narrative *.md or *.txt
  handoff, session, resume, status, report, plan, objective files
  loose *.png, *.jpg, *.json, *.csv, *.log
  evidence/, tmp/, out/, logs/, screenshots/, archive/, backup/
  *_old, *_v2, *.bak
  one-off scripts
```

## Untracked files

Git cannot restore a file it never tracked. For an untracked candidate:
move it to an out-of-tree quarantine directory, list the path in the
report, and delete only after the user confirms.
