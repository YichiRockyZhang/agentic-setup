---
name: lineups-deslop-reduce
description: Shrink Lineups stack code (Lineups UE 5.3 repo, LineupsAscentLab, LineupsAscentLabWork) while every gate verdict and every golden output stays identical — dead UE and native C++, dead C#, unused Build.cs dependencies, includes, usings, and members, near-duplicate launchers and evidence checkers, copied probe code across Work lanes, redundant PowerShell and Python scripts. Wraps `deslop-reduce` with the stack's analyzers, duplicate families, and GPU timing check. Use for any shrink, dedupe, dead-code, or simplification work in those trees, or as the reduce pass of `lineups-deslop`.
license: MIT
---

# Lineups Deslop: Reduce

Follow `deslop-reduce` in full. This skill adds the stack facts. The
profile for each tree in scope defines its gates and "same result":

- Lab trees: [../lineups-deslop/references/ascentlab.md](../lineups-deslop/references/ascentlab.md)
- Lineups repo: [../lineups-deslop/references/lineups.md](../lineups-deslop/references/lineups.md)

Precondition: the checkpoint from `lineups-deslop` step 2 exists, and the
baseline gate outputs are saved.

## Find candidates

Delegate these scans to a cheaper model; review every hit yourself.

- **UE C++**: stale `Build.cs` dependencies, public dependencies that can
  be private, headers that include what a forward declaration would do,
  local reimplementations of `FPaths` or `FString` utilities. Reflected
  symbols can be reached by name from assets, config, console commands,
  and commandlet modes — check before you delete one.
- **Native C++**: the builds use `/W4`. Look for exported functions nothing
  imports, and for Windows or D3D setup code copied between trees.
- **C#**: run the Roslyn analyzers as a report, without applying fixes:
  `dotnet format analyzers <project> --verify-no-changes --diagnostics IDE0051 IDE0052 IDE0005 IDE0060 --severity info`.
  Reflection, JSON serialization, and source generators reach members by
  name.
- **PowerShell and Python**: `Invoke-ScriptAnalyzer -Path . -Recurse -Severity Warning`
  when PSScriptAnalyzer is installed; search for dot-sourcing, `&` calls,
  and gate or doc references before you delete a script. A `.ps1` and a
  `.py` with the same stem are a duplicate-family candidate.
- **Cross-tree copies**: compare files with the same name or purpose across
  the lab root, every Work lane, and Lineups tools.

## Duplicate families

Merge only after the merged version passes every gate on the same inputs as
the originals, then delete the originals in the same change. Check whether
these still exist:

- Two restricted-token launchers in the lab (`isolate.cpp` with
  `run-isolated.ps1`, and the reimplementation inside
  `run-isolated-scene.ps1`).
- Two lab evidence checkers (`check-evidence.ps1` and
  `check-evidence-scene.ps1`).
- Probe code copied across Work lanes: container mounting, package lookup,
  D3D device setup.
- Same-stem script pairs in Lineups `scripts/` where only one is
  gate-referenced.

A merge that needs new shared code or a new interface goes to
`lineups-deslop-organize` Part B for the user to choose; this pass only
deletes and inlines.

## Prove it

After every deletion step:

1. Rebuild. The compiler warning list must not grow.
2. Run the gates that cover what you touched; run all of them for shared
   code.
3. Compare with the profile's equality rules.
4. For code on a GPU, readback, or per-frame path, compare median timing
   over three runs.

Revert any step whose comparison differs, and find out why before you try
again.
