---
name: deslop-organize
description: Reorganize a codebase so every file has one obvious home — sweep stray Markdown, JSON, PNG, log, script, test, evidence, scratch, and backup files out of the root and the tree, enforce one README per module and one indexed doc per topic, and deepen shallow modules and fix dependency direction. Changes where things live, never what code computes. Use for repo cleanup, root clutter, doc sprawl, "where should this file go", folder restructuring, module boundary or architecture review, onboarding pain, or as the organize pass of `deslop`.
license: MIT
---

# Deslop: Organize

**Every file has one owner and one home. The root says what the project is
and nothing else.**

This pass moves, merges, and deletes files and reshapes module boundaries.
It does not change what any code computes. The only code edits allowed are
the ones a move forces: include paths, import paths, build-manifest entries,
and doc links. Code shrinking belongs to `deslop-reduce`.

## Part A — Hygiene

### A1. Root allowlist

The root holds only what a newcomer needs in the first minute:

- One `README`, one `LICENSE`, and where they apply one each of
  `CONTRIBUTING`, `SECURITY`, `CODE_OF_CONDUCT`.
- Agent instruction files: one `AGENTS.md` and/or one `CLAUDE.md`. No dated
  or versioned copies.
- Build and package manifests, and the project descriptor the toolchain
  requires at the root.
- Tool dotfiles (`.gitignore`, `.gitattributes`, `.editorconfig`, format and
  lint configs) and ownership metadata (`CODEOWNERS`).
- Top-level directories, each with one purpose you can say in five words.

Well-kept large projects keep 9 to 40 root files, and almost none of them
are narrative docs. Anything else at the root moves or goes. If the project
already states its own root allowlist, use that one.

### A2. Stray-file sweep

A stray file is any file without an owner: nothing reads it, no index
links it, or it sits far from the thing it describes. Sweep the whole tree,
not only the root. Typical strays:

- **Markdown**: handoffs, session reports, resume or status notes, plans,
  objective copies, review write-ups, duplicate state files.
- **JSON, YAML, CSV**: one-off dumps, probe output, copied configs.
- **Images**: screenshots, evidence captures, debug renders.
- **Scripts**: one-off or near-duplicate `.ps1`, `.sh`, `.py`, `.cmd`
  files.
- **Backups**: `*_old`, `*_v2`, `*.bak`, `*.orig`, `copy of *`. Git is the
  only checkpoint.
- **Folders**: `evidence/`, `tmp/`, `out/`, `logs/`, `screenshots/`,
  `archive/`, `old/`, `backup/`, parallel `Config/` and `configs/`.

Decide each file with [references/placement.md](references/placement.md).
The short form:

1. **Something reads it** — code, a gate, a build step, or an indexed doc.
   Keep it, and move it next to its reader or into the data directory that
   owns that kind of file.
2. **It is generated** — delete it and gitignore its output path. Point the
   generator at one ignored output directory.
3. **It is knowledge** — merge the live facts into the one doc that owns the
   topic, then delete the file. Session state and handoffs leave the tree:
   the project's single state file if it defines one, otherwise an
   out-of-tree notes directory.
4. **It is evidence** — keep it only if a maintained doc cites it by path.
   Otherwise move it out of the tree.
5. **Nobody can say** — quarantine it out of tree and ask.

### A3. Docs

- **One README per module or submodule**, next to its code: what the module
  is, how to build or run it, where its tests are. Nothing else.
- **One maintained doc per topic.** Link to it; never copy its content.
  When two docs cover one topic, merge them and delete the loser.
- **One index.** Every doc is reachable from one index file (a docs
  `README`, a routing doc, a toctree). A doc the index does not reach is an
  orphan: link it, merge it, or delete it.
- **Stale prose is deleted, not appended to.** A doc describes the current
  truth. History lives in git.
- **Doc-only images live beside the doc** in an assets folder the doc
  links to. An unlinked image is a stray.

### A4. Proof for hygiene

- Every relative link in every Markdown file resolves.
- No moved or deleted path appears anywhere in code, scripts, CI, manifests,
  or docs.
- The gates from the `deslop` baseline still pass.
- The root listing matches the allowlist.

## Part B — Architecture

Vocabulary. Use these exact words, because loose synonyms hide the
distinction the pass depends on:

- **Module**: anything with an interface and an implementation — function,
  file, folder, plugin, package.
- **Interface**: everything a caller must know — types, invariants, units,
  error behavior, performance facts.
- **Depth**: behavior a caller gets per unit of interface it must learn.
  Deep is good.
- **Seam**: a place where you can change behavior without editing that
  place.
- **Locality**: how concentrated the knowledge about one decision is.

### B1. Find candidates

Look first where the pain is: files with the most recent churn, and places
where one concept makes you bounce across many small files. Signals:

- A **shallow module**: its interface is as complicated as its
  implementation. Pass-through wrappers, forwarding methods, one-line
  `Helper` or `Manager` classes.
- The **deletion test** fails the other way: removing a module only
  relocates its complexity, so it was never earning its interface.
- A **hypothetical seam**: an interface or base class with one
  implementation and no second one coming. One adapter is hypothetical;
  two adapters make a real seam.
- **Wrong dependency direction**: stable core code depends on volatile or
  experimental code, or two modules depend on each other.
- **Grab-bag names**: `utils`, `common`, `misc`, `helpers`, `Manager` — the
  name says nothing because the contents share nothing.
- **Tests far from code** relative to the ecosystem convention, or test
  helpers that exist only to reach private internals.

### B2. Present, then act

Architecture changes interfaces, and interfaces are the user's call.
List candidates with the files involved, the problem, the proposed shape,
and a strength: **Strong**, **Worth exploring**, or **Speculative**. Let the
user pick. Do not propose new interfaces in the first list — first agree on
which problem to solve.

For a chosen candidate, sketch at least two alternative interfaces before
you pick one; the first idea is rarely the best. Parallel subagents with
different design pressures (smallest interface, most common caller,
most flexible) do this well.

### B3. Execute

- Move with the VCS (`git mv`) so history follows the file.
- Commit moves alone. Commit the edits the move forces as a separate commit.
- Update every reference, the doc index, and the module README.
- Record a decision in a short architecture decision record only when a
  future agent would otherwise re-propose the rejected option.

## Rationalization check

| If you hear yourself say... | Do this instead |
|---|---|
| "It's just a notes file, it doesn't hurt" | Name its reader. No reader, no file. |
| "I'll leave the old copy in case" | Git is the backup. Delete it. |
| "This evidence might matter someday" | Cite it from a maintained doc today, or move it out of the tree. |
| "A `utils` folder is fine for now" | Name the concept, or put each function with its only caller. |
| "Moving and fixing in one commit is faster" | Faster to write, impossible to review. Split it. |
