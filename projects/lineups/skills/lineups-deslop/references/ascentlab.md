# AscentLab profile

Durable facts for deslopping `LineupsAscentLab` and `LineupsAscentLabWork`.
Current file lists go stale; discover them with a survey each run. If a fact
here no longer matches the trees, fix this file in the same change.

## Contents

- Trees
- Hard boundaries
- Gates
- "Same result" equality rules
- Move traps
- Target layout
- Doc roles

## Trees

| Tree | Role |
|---|---|
| `C:\Users\rocky\Unreal Projects\LineupsAscentLab` | Harness: `AscentLab.csproj` and root C# sources, native `gpu.cpp` and `isolate.cpp`, build/run/check scripts, `evidence/`, `isolated/`, vendored `lib/` DLLs |
| `C:\Users\rocky\Unreal Projects\LineupsAscentLabWork` | Candidate code lanes, one folder each (for example `scene-render`, `bindings`, `preshader`, `draw`, `cascade-sim`), plus a worker brief |

Neither tree is under version control. See the checkpoint rule in
`lineups-deslop`.

## Hard boundaries

These hold for every pass. A cleanup that crosses one is not a cleanup.

- Never start, attach to, or read the memory of the Valorant game process.
- Riot containers under `C:\Riot Games\VALORANT\live\ShooterGame\Content\Paks`
  are read-only input. Never copy them.
- Never persist asset payloads anywhere in the trees: `.uasset`, `.umap`,
  `.ubulk`, `.uexp`, `.pak`, `.utoc`, `.ucas`, `.dds`, `.fbx`, `.dxbc`,
  `.uecode`. `check-evidence.ps1` fails on them.
- Production `Lineups`, the engine install, and `resume-0551` evidence are
  read-only references. Never edit them from this skill.
- ACL changes stay inside `LineupsAscentLab`. The run scripts grant
  `BUILTIN\Users` rights on the lab; do not widen that.
- No Unreal Editor GUI.

## Gates

Run serially, from the lab root unless noted. Stop at the first nonzero
exit.

| # | Gate | Command |
|---|---|---|
| 1 | Native GPU build | `.\build-gpu.cmd` |
| 2 | Native isolation launcher build | `.\build-isolation.cmd` |
| 3 | Managed build | `dotnet build .\AscentLab.csproj -c Release --nologo` |
| 4 | Work lane builds | `powershell -NoProfile -ExecutionPolicy Bypass -File .\autonomy-build.ps1`, plus `dotnet build -c Release --nologo` in every other Work lane with a `.csproj` |
| 5 | Restricted surface run | `.\run-isolated.ps1` |
| 6 | Surface evidence check | `.\check-evidence.ps1` |
| 7 | Restricted scene run (GPU) | `.\run-isolated-scene.ps1 -Launch` |
| 8 | Scene evidence check | `.\check-evidence-scene.ps1` |

Current managed warnings (CS9107, CS8602) are baseline, not new failures.
Compare warning lists before and after; a new warning is a regression.

When a script moves, update this table in the same change.

## "Same result" equality rules

Compare these between baseline and after:

- Every gate exit code.
- `evidence/probe.json` stage verdicts: no `failure` stage; isolation
  restricted with 8 denials at HRESULT -2147024891; 11 uploaded mips and 11
  GPU-readback-matched mips; 54 shader records created.
- `evidence/source-reads.jsonl`: every read is a `.pak`, `.utoc`, or `.ucas`
  inside the container directory. The set of container files read is equal.
- Isolated scene PNGs: SHA-256 of every PNG under `isolated/evidence` is
  equal. The acceptance record expects 22 byte-identical PNGs.
- Scene isolation probe: every deny-listed path still denied.

These change by design and are not evidence of a behavior change:

- Source file hashes, line counts, and byte counts in the receipts that
  `check-evidence.ps1` writes. They hash the source you just edited.
- Timing and peak working-set numbers. For code on the GPU or readback
  path, run gate 5 or 7 three times before and after; a median slowdown
  beyond the run-to-run spread is a regression.
- Log text and timestamps.

`evidence/checkpoint.json` predates the current shader and isolation code.
Never use its hashes as a baseline.

## Move traps

Grep is not enough here. Check each of these before any move:

- **Scripts resolve the lab root from their own location**
  (`$labRoot = $PSScriptRoot`). Moving a script into a subfolder changes
  its root. Replace with `Split-Path $PSScriptRoot -Parent` in the same
  move-forced edit.
- **`isolate.exe` hardcodes the managed entry point**
  `<root>\bin\Release\net10.0\AscentLab.dll`. Moving `AscentLab.csproj` or
  changing its output path breaks the restricted launch until you rebuild
  `isolate.cpp` with the new path.
- **SceneRender compiles absolute lab and Work paths in as constants.**
  Moving or renaming either tree, `isolated/`, or a Work lane breaks it
  silently. Search the Work lanes for the old absolute path before any
  directory move.
- **`check-evidence-scene.ps1` hardcodes `isolated\evidence`,
  `isolated\scene-render-bin`, and the Work path.**
- **`native/` holds build output from `build-*.cmd`**, while the native
  sources sit at the root. Do not move sources into `native/`; it is
  output.
- **`lib/` DLLs are the parser closure** with recorded hashes in
  `metadata/dependencies.json`. Moving them changes load paths; renaming
  or replacing them changes the recorded closure.

## Target layout

The direction for the organize pass. Propose it; the user approves each
step.

```
LineupsAscentLab/
  README.md            what this is, reproduce steps, one-line status, links to docs/
  AscentLab.csproj
  src/                 managed sources (Program.cs, IsolationCheck.cs, ...)
  src/native/          gpu.cpp, isolate.cpp
  scripts/             build-*, run-*, check-* scripts
  docs/                maintained topic docs (acceptance, isolation, shader contracts)
  third_party/         vendored DLLs + their provenance and hash record
  evidence/            only the current evidence a maintained doc cites
  (ignored)            bin/ obj/ native build output, isolated/, scratch

LineupsAscentLabWork/
  README.md            one line per lane: purpose, status, entry point
  <lane>/README.md     purpose, build, run, where its output goes
  <lane>/...           lane code; output goes to an ignored folder
```

Lanes that finished — promoted into the lab, or abandoned — are deleted
after their result is recorded in a maintained doc. A lane is not an
archive.

## Doc roles

Each role has exactly one file. When two files claim the same role, the
contradiction is itself a slop signal: merge the true facts into the one
owner and delete the rest.

| Role | Owner |
|---|---|
| Entry point: what, how to reproduce, one-line status | `README.md` |
| Current acceptance truth: requirements, evidence, limits | one acceptance doc under `docs/` |
| Isolation design and host execution findings | one isolation doc under `docs/` |
| Open work and lane coordination while lanes are active | one state or handoff file; deleted when the work closes |
| Original objective | the acceptance doc's requirements section, not a separate `*.original.md` |
| Session handoffs, resume notes, review write-ups | merge live facts into the owners above; the rest leaves the tree |

The README must not contradict the acceptance doc. When status changes,
update both in the same change, or make the README link instead of
restating.
