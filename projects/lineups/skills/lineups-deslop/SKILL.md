---
name: lineups-deslop
description: Deslop the Lineups stack — the Lineups Unreal Engine 5.3 repo and the Ascent direct-container lab (LineupsAscentLab and its sibling LineupsAscentLabWork). Same stack across all three — C++ (UE and native cl builds), C#/.NET, PowerShell and cmd scripts, JSON/CSV registers, and generated evidence. Wraps the general `deslop` skill with each tree's checkpoint rule, gates, hard boundaries, and move traps. Use whenever cleanup, decluttering, reorganizing, shrinking, or deslopping touches Lineups, LineupsAscentLab, or LineupsAscentLabWork — root Markdown sprawl, handoff and status files, evidence and output folders, stray PNG or JSON files, duplicate scripts, dead C++ or C# code.
license: MIT
---

# Lineups Deslop

This skill adds the Lineups stack's facts to the general `deslop` skill.
Load `deslop` first and follow its workflow. Where the two disagree, this
skill wins for these trees.

Sub-skills, in the order `deslop` runs them:

- `lineups-deslop-organize` — `deslop-organize` plus layouts, doc roles,
  and move traps.
- `lineups-deslop-reduce` — `deslop-reduce` plus analyzers, duplicate
  families, and timing checks.

## 1. Pick the profile

Each tree has a profile with its gates, boundaries, and traps. Read the one
for every tree in scope before you touch anything:

| Tree | Profile |
|---|---|
| `LineupsAscentLab`, `LineupsAscentLabWork` | [references/ascentlab.md](references/ascentlab.md) |
| `Lineups` | [references/lineups.md](references/lineups.md) |

Treat the lab and its Work sibling as one system when you move or delete
anything: lab scripts build and launch Work lanes, and Work code has lab
paths compiled in.

## 2. Checkpoint first

Every move and delete must be undoable.

- **Lineups** is a git repo. Start from a clean worktree, or a branch or
  worktree of its own. Never mix cleanup into someone's uncommitted work.
- **The lab trees are not under version control.** Before the first move
  or delete, ask the user to pick one:
  1. **Initialize a local git repository** in each lab tree, with a
     `.gitignore` for build output (`bin/`, `obj/`, native build products)
     and runtime output (`isolated/`, generated evidence). Commit
     everything else as the checkpoint. Recommended: it gives you
     `git mv`, diffs, and rollback, and nothing leaves the machine. Add no
     remote.
  2. **A full out-of-tree snapshot** — for example
     `robocopy <tree> <snapshot-dir>\<tree>-<date> /E`, excluding `bin` and
     `obj` — plus a file-hash manifest of what you copied.

  Do not start without one. Record it in the final report.

## 3. Baseline

Run the profile's gates in order, serially: Unreal builds, commandlets,
and GPU runs must never overlap. Save outputs, verdicts, and golden hashes
to scratch outside the trees. If any gate is red before you start, stop
and report.

## 4. Run the passes

Follow `deslop` step 4 with the two `lineups-` sub-skills. After each pass,
rerun the gates and compare against the baseline with the profile's
equality rules.

## 5. Report

Use the `deslop` report. Add the checkpoint used, per-gate before and after
verdicts, golden-output equality, and which receipts changed by design
(source hashes, timing) and why that is not a behavior change.
