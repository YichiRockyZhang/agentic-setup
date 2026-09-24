# Lineups profile

Deslop facts for the `Lineups` repo
(`C:\Users\rocky\Unreal Projects\Lineups`). The repo's own documents own
most of these rules; this file points at them instead of copying them. Read
the owners before any pass. If this file and an owner disagree, the owner
wins — then fix this file.

## Owners

| Topic | Owner |
|---|---|
| Project policy, hard boundaries, standing traps | `AGENTS.md` |
| Architecture and document lifecycle (which doc lives where, size caps, what counts as historical) | `docs/SYSTEM_SPEC.md` |
| Topic routing — the doc index | `docs/ROUTING.md` |
| Gate commands and required checks | `data/gates.json`, run through `scripts/run_gate.ps1` |
| Current status and open work | `STATE.md` |

## Hard boundaries

From `AGENTS.md` Article I. A cleanup that crosses one is not a cleanup.

- Never touch the Valorant game process.
- No Unreal Editor GUI; headless commandlets and automation only.
- Never commit extracted assets (`Content/` map trees, `*.uasset`,
  `*.umap`, paks). Keep `.gitignore` enforcing that; extension-based rules
  have slipped before.
- The engine install is read-only.
- Never invent or relocate physics constants out of their provenance
  domain. A register value moves only with its provenance.

## Gates

- `scripts/run_gate.ps1 -Gate build` for any C++ change.
- `scripts/run_gate.ps1 -Gate tests` with the bare `Lineups` filter.
- Any other gate that covers what you touched, by name from
  `data/gates.json`. Do not copy gate commands or test counts into this
  file; read them from the manifest.
- Serialize Unreal builds, commandlets, and GPU work.

"Same result" means every gate you ran has the same verdict and the same
measured values as the baseline, within the tolerance the gate itself
defines.

## Doc placement

`docs/SYSTEM_SPEC.md` §5 owns where each kind of doc lives and its size
cap. `docs/ROUTING.md` must link every maintained doc; a doc it does not
reach is an orphan to link, merge, or delete. Update both in the same
change as any doc move. Frozen snapshots named there stay byte-frozen.

The root allowlist for this repo is not decided yet. Until `SYSTEM_SPEC.md`
§5 changes, propose root moves to the user; do not make them.

## Move traps

- `docs/ROUTING.md`, `STATE.md`, and topic docs cite paths — grep them for
  every moved path.
- Gate commands in `data/gates.json` name script paths.
- `.gitignore` rules are partly extension-based; moving a file can change
  whether it is ignored. Check `git check-ignore -v` after each move.
- Asset paths are Riot package-derived; never rebuild one from parts.
- `Config/` (Unreal engine ini files) and `configs/` (Lineups data
  registers) look like duplicates but are not. `Config/` is an Unreal
  reserved name.
- Retired programs belong in `docs/archive/` per `AGENTS.md`; that folder
  is sanctioned, not a stray.

## Reduce specifics

- A tooling port proves schema, verdict, and exit-code parity on the same
  fixtures before the original is deleted (`AGENTS.md` Article III).
- Reflected UE symbols (`UFUNCTION`, `UPROPERTY`, classes) can be reached
  from assets, config `.ini` files, console commands, and commandlet mode
  strings. Grep configs and scripts before deleting one.
- Scripts not named in `data/gates.json` are not automatically dead; many
  are run by hand per a topic doc. Cross-check `docs/` before deleting a
  script.
