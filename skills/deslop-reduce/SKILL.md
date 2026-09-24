---
name: deslop-reduce
description: Delete as much code as possible while the observable result stays exactly the same — production-safe code golf in the extreme-programming sense. Captures a behavioral baseline, deletes in small verified steps (dead code, duplicate logic, pass-through layers, speculative generality, unused dependencies and includes), and proves equality after. Use when asked to shrink, slim, simplify, minimize, dedupe, or delete code, remove dead code or bloat or AI slop, cut lines of code, or as the reduce pass of `deslop`.
license: MIT
---

# Deslop: Reduce

**The best code is the code you deleted without anyone noticing.**

Every line is something to read, test, debug, and keep correct. This pass
removes lines until the next deletion would cost meaning. It works inside
the existing module boundaries; moving files and reshaping modules belongs
to `deslop-organize`.

## The Iron Law

```
CAPTURE THE BEHAVIOR. DELETE ONE STEP. PROVE IT UNCHANGED. REPEAT.
```

A deletion without a before-and-after proof is not a cleanup. It is an
untested change with a nice diff stat.

## What to shrink, and what to keep

Shrink the **logic surface**: branches, duplicate paths, dead code, layers,
parameters, dependencies.

Keep the **explanatory surface**: names that carry meaning, interface
contracts, units and coordinate frames, and "why" comments on non-obvious
decisions. Cutting the one sentence that explains a strange constant does
not reduce the code; it corrupts the record. Delete comments that restate
the next line, apologize for a bad name, or narrate history git already
holds.

## Procedure

1. **Pin the behavior.** Use the baseline from `deslop` if it exists. For a
   unit with no check, write a characterization check first: feed it
   representative inputs and assert the current outputs.
2. **Prove the net catches things.** Break the target code on purpose — flip
   a condition, delete a line — and confirm a check fails. Restore it. A
   suite that stays green on broken code proves nothing about your
   deletion. Mutation testing does this at scale where the stack has it.
3. **Check the fence.** Before you delete a symbol, find why it exists:
   `git log -S`, references, reflection or string-based lookups, config
   keys, build scripts. Code reached by name at runtime looks dead to grep.
4. **Delete one move** from the catalog below.
5. **Build and run the gate.** Any diff in output is a regression, not a
   simplification. Revert and understand it.
6. **Repeat** until the stop rule fires.
7. **Measure and report** the delta: lines, files, dependencies, build time.
   Numbers are evidence, not the goal.

Delegate the finding work — dead-symbol lists, duplicate detection,
unused-dependency scans — to a cheaper model or to the stack's analyzers.
Review every deletion yourself.

## Catalog, highest payoff and lowest risk first

1. Remove dead code: unreachable branches, unreferenced functions, types,
   and files.
2. Remove unused includes, imports, and module or package dependencies.
3. Delete commented-out code and stale TODOs. Git keeps history.
4. Remove unused parameters, fields, config keys, and flags that nobody
   varies.
5. Inline a function or class that only forwards to another (remove the
   middle man).
6. Collapse an interface or base class with a single implementation.
7. Merge duplicate logic into one place — but only after two or three real
   copies exist, and only if they change for the same reason.
8. Split a flag argument into two named functions, or delete the dead side.
9. Replace a hand-rolled helper with the standard library or framework
   equivalent.
10. Delete defensive checks the type system or an upstream invariant already
    guarantees. Fix the data shape so the check has nothing to catch.
11. Delete error handling that swallows failures silently. Let it fail loud
    at one boundary.
12. Delete the old path after a migration proves parity on the same
    fixtures.
13. Flatten nesting with guard clauses; decompose tangled conditionals.
14. Consolidate near-duplicate tests into one table-driven test.
15. Language idioms that shorten code — last, and only when the result reads
    faster than the original.

## Stop rule

Stop deleting when the next deletion would:

- make a reader re-derive intent instead of reading it,
- merge two distinct concepts into one symbol just to save lines, or
- trade "reveals intention" for "fewer elements". Intention ranks higher.

If a reviewer needs you to explain what a line used to do in order to
understand what it does now, you went too far.

## Performance-sensitive code

In hot paths — per-frame, per-element, per-sample loops — measure before and
after. Fewer lines through extra indirection (virtual dispatch, heap-built
strategy objects, generic layers) can cost more time than it saves reading.
Equal output with a measured slowdown is not the same result.

## Slop signals

- Wrappers that add a name and nothing else.
- `try`/`catch` at every layer, each swallowing or re-wrapping the same
  error.
- Validation repeated at every call site instead of once at the boundary.
- A near-duplicate helper added beside an existing one.
- Config knobs, extension points, and parameters with one value in
  practice.
- Comments that restate code in English.
- Placeholder branches, skipped tests, and stubs presented as finished work.

Stack-specific analyzers and dead-code tools:
[references/tooling.md](references/tooling.md).
