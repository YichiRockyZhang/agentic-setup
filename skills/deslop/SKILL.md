---
name: deslop
description: Clean up a sloppy codebase in two separate passes — organize it (layout, module boundaries, stray files, doc placement) and reduce it (delete as much code as possible with the exact same observable result). Use whenever the user says deslop, anti-slop, clean up the repo, declutter, tidy, simplify the codebase, make this repo pleasant for a new engineer, remove AI slop, delete dead code, or complains about stray Markdown, JSON, PNG, test, evidence, or scratch files lying around — even if they only name one of the two passes.
license: MIT
---

# Deslop

**A new engineer opens the repo and it is a breath of fresh air.** Every file
has an obvious home, every home has one purpose, and no line exists without
a reason.

This skill is the conductor. It does no cleanup itself. It scopes the work,
captures proof of the current behavior, and runs two sub-skills in a fixed
order:

| Sub-skill | Changes | Never changes |
|---|---|---|
| `deslop-organize` | Where things live and how they are bounded: files, folders, docs, module seams | What any code computes |
| `deslop-reduce` | How much code exists inside a boundary | Where a boundary is; the observable result |

The split is the point. A move and a rewrite in the same step cannot be
reviewed: rename detection breaks, and a behavior change hides inside a
diff that looks like a relocation. One pass moves, the other deletes.

## The Iron Law

```
SAME OBSERVABLE RESULT, PROVED BEFORE AND AFTER.
NO PROOF, NO CLEANUP.
```

"Same result" means: the same gates pass, golden outputs are byte-identical
or equal under a stated tolerance, and public interfaces are unchanged
unless the user approves the change.

## Workflow

### 1. Scope

Agree on the target: whole repo, one directory, or one module. Read the
project's instruction files (`AGENTS.md`, `CLAUDE.md`, `CONTEXT.md`) and its
doc index first. Project rules override this skill where they conflict. If a
project-specific deslop skill exists (for example `<project>-deslop`), load
it too: it names the gates, the root allowlist, and the protected paths.

### 2. Baseline

Record the evidence that later proves "same result". Save it outside the
repo, in a scratch directory the harness provides.

- The gate commands (build, tests, lint) and their exit codes and summaries.
- Golden output for anything the tests do not cover: CLI output, generated
  files, screenshots with a pixel-diff tolerance. If a behavior has no
  check, write a characterization check now — it pins what the code does,
  not what it should do.
- Size metrics: tracked file count, root entry count, Markdown count, lines
  of code per top-level source directory (`tokei` or `cloc`).

If a gate already fails, stop and report. You cannot prove "same result"
from a red baseline.

### 3. Survey

Build two queues. Delegate the rote parts — directory walks, reference
greps, line counts — to a cheaper model with an exact output format, then
check a sample of its claims yourself.

- **Organize queue**: stray files, misplaced docs, orphan docs, duplicate or
  confusing folders, shallow modules, dependency cycles.
- **Reduce queue**: dead code, duplicate logic, pass-through layers,
  speculative generality, unused dependencies and includes.

Keep the queues in scratch, not in the repo.

### 4. Run the passes in order

1. **`deslop-organize`, hygiene part** — stray files, doc placement, root
   cleanup. Cheap, mostly non-code, and it removes the noise that hides
   real code problems.
2. **`deslop-reduce`** — delete within the current boundaries.
3. **`deslop-organize`, architecture part** — module deepening and seam
   moves. Reduction first means you never carefully relocate dead code.
   Present candidates and let the user choose before you change any
   interface.
4. **`deslop-reduce` again**, only on modules that step 3 touched.

Run the gates after every pass. Keep each pass in its own commits: one
logical change per commit, moves never mixed with edits.

### 5. Report

Show a before/after table: tracked files, root entries, Markdown files,
lines of code, gate status. List what you deleted, what you moved and where,
and every open decision that needs the user. Nonblocking findings you did
not act on go into the project's owning queue or doc, not into a new file.

## Guardrails

- **Chesterton's fence.** Before you delete a file or a symbol, find out why
  it exists: `git log --follow`, references, the doc that cites it. Unknown
  purpose means ask, not delete.
- **Untracked files are unrecoverable.** Git cannot restore a file it never
  tracked. Move untracked candidates to an out-of-tree quarantine directory
  and report the path; delete only after the user confirms.
- **References before removal.** Grep for the full path, the basename, and
  the stem before you move or delete any file. Code, scripts, docs, CI
  config, and build manifests all count.
- **Generated and ignored directories are out of scope** unless the user
  names them: build output, caches, vendored third-party code.
- **Stop after two bounded failures** with the exact error and the next
  diagnostic. Do not grind.

## Related skills

- `torvalds-doctrine` — data shape first, dumbest obvious code, the review
  vocabulary for slop.
- `rat-principle` — one stray file or dead function means a generator made
  more; find and fix the generator.
