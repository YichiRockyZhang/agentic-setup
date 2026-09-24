---
name: lineups-deslop-organize
description: Organize the Lineups stack trees (Lineups, LineupsAscentLab, LineupsAscentLabWork) — collapse Markdown sprawl (handoffs, resume and status notes, acceptance and review write-ups) into one doc per role, sweep stray PNG, JSON, log, and out files and evidence or output folders, move sources and scripts into a clean layout without breaking hardcoded paths, and give each module or Work lane one README. Wraps `deslop-organize` with each tree's doc owners and move traps. Use for any organize, restructure, declutter, or doc cleanup in those trees, or as the organize pass of `lineups-deslop`.
license: MIT
---

# Lineups Deslop: Organize

Follow `deslop-organize` in full. This skill adds the tree facts. Read the
profile for every tree in scope first — its move traps, layout, and doc
roles:

- Lab trees: [../lineups-deslop/references/ascentlab.md](../lineups-deslop/references/ascentlab.md)
- Lineups repo: [../lineups-deslop/references/lineups.md](../lineups-deslop/references/lineups.md)

Precondition: the checkpoint from `lineups-deslop` step 2 exists. Without
it, stop.

## Hygiene, in this order

1. **Docs by role.** Map every Markdown file in scope to one role (lab: the
   profile's doc-roles table; Lineups: `docs/SYSTEM_SPEC.md` §5). For each
   role with more than one file:
   - Find contradictions between the files (status, which file is
     authoritative, stage verdicts). Evidence beats prose, and newer
     evidence beats older. Settle a disputed claim against evidence or a
     gate run, not against another doc.
   - Merge the true, current facts into the owner. Delete the rest, or
     move session history out of the tree if the user wants to keep it.
   - Fix every link that pointed at a removed file, and update the doc
     index (`docs/ROUTING.md` in Lineups).
2. **Evidence.** Keep an evidence file only if a maintained doc cites it
   and it is current. Evidence a doc already calls historical or stale
   leaves the tree. Runtime output (logs, `.out` files, isolated runs) is
   ignored output, not evidence, unless a doc cites a specific file.
3. **Stray PNG and JSON.** Keep one only when a gate or program reads it or
   a maintained doc cites it. Record golden-output hashes before you touch
   a folder that holds them.
4. **Modules and lanes.** Each module, plugin, tool, or Work lane gets one
   `README.md`: purpose, status, build, run, output location. Ask the user
   about lanes that look finished or abandoned before deleting them.

## Layout moves

Only after hygiene, one move group at a time, and only through the
profile's move traps. For the lab, the target layout in its profile gives
the order: scripts, then managed sources, then native sources, then
vendored DLLs with the user's approval. For Lineups, propose root and
layout moves to the user; its root allowlist is not decided yet.

Never rename or move a tree, a Work lane, or a plugin folder without first
searching for compiled-in absolute paths and path-derived constants.

Run the gates after each move group. Commit each move group, then the
edits it forced, separately.
