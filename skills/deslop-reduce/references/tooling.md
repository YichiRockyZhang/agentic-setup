# Reduction tooling by stack

Analyzers find candidates. They do not prove a deletion is safe; the gate
does. Every tool below has false positives around reflection, code
generation, and name-based lookup.

## Metrics

- `tokei` or `cloc` — lines per language and directory, before and after.
- `git ls-files | wc -l` — tracked file count.

## C and C++

- Compiler warnings: `-Wunused` family (GCC and Clang), `/W4` (MSVC).
- `clang-tidy`: `clang-analyzer-deadcode.DeadStores`, `misc-unused-*`,
  `readability-redundant-*`.
- include-what-you-use for superfluous includes.
- Linker map or `--gc-sections` reports for symbols nothing references.

## Unreal Engine C++

- Reflection hides callers. A `UFUNCTION`, `UPROPERTY`, or class can be
  referenced from Blueprint assets, config `.ini` files, console commands,
  or string lookups. Grep configs and scripts, and check asset references
  before you delete a reflected symbol.
- `Build.cs`: every entry in `PublicDependencyModuleNames` and
  `PrivateDependencyModuleNames` must map to a real include. Prefer private
  dependencies; move to public only when a public header needs the type.
- Prefer engine utilities (`FPaths`, `FString` functions, containers) over
  local reimplementations.
- Forward-declare in headers; include in `.cpp` files.

## C# and .NET

- Roslyn analyzers: `IDE0051` (unused private member), `IDE0052` (unread
  private member), `IDE0005` (unnecessary using), `IDE0060` (unused
  parameter). Set severity in `.editorconfig`; apply with `dotnet format`.
- Source generators and reflection can reference members the analyzer
  flags. Check before deleting.

## PowerShell

- PSScriptAnalyzer: `Invoke-ScriptAnalyzer -Recurse -Severity Warning`.
  `PSUseDeclaredVarsMoreThanAssignments` flags unused variables but has
  false positives in script blocks.
- Search for dot-sourcing and `&` invocations before deleting a script.

## Python

- `vulture` for dead code; `ruff` rules `F401` (unused import) and `F841`
  (unused variable).

## JavaScript and TypeScript

- `knip` for unused files, exports, and dependencies; `ts-prune` for unused
  exports.

## Behavior proof

- Characterization or approval tests pin current output.
- Mutation testing (Stryker, mutmut, cargo-mutants, Mull) confirms the
  suite catches real breakage.
- Coverage tools show whether a branch is unreachable or only untested.
  Untested is not dead.
