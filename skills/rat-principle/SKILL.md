---
name: rat-principle
description: Fix the class, not the instance — one visible bug means ten more in the walls. Use when fixing any bug, test failure, or flake, writing a regression test, reviewing or triaging a patch, or whenever you notice one instance of anything wrong — one weird record, one broken call site, one stale doc, one crash report.
license: MIT
---

# The Rat Principle

**If you see a rat, there are ten in the walls.**

The bug you can see is not the bug. It is a sample from a population,
produced by a generator. Fixing the sample is setting a trap for one rat
and declaring victory while the colony keeps breeding behind the drywall.

## The Iron Law

```
NO INSTANCE FIXES.
A fix that covers only the reported case is not a fix —
it is a decision to leave the rest of the class in production.
```

## Why this is true

Bugs do not materialize at the place they surface. They are manufactured
upstream by a **generator** — a wrong assumption, an unvalidated boundary,
a copy-paste lineage, a missing invariant. Generators are machines: they
produce populations, not individuals. The one rat you saw is simply the
one that wandered into view.

Corollary: the reported symptom tells you where to *look*, never where to
*cut*.

## The workflow

Phases are gates, not a checklist. Do not open the next gate until the
current one is true.

### Phase 1 — Confirm the rat

Build the tightest loop that goes red on *this* bug: one named command,
already run, output in hand. Fast, deterministic, asserts the exact
symptom — not "didn't crash". If no red-capable command exists, building
it **is** the work; do not proceed to theories.

### Phase 2 — Find the generator

Follow the data upstream. Where was the bad value born? What assumed
something false? Keep tracing until you reach something that *produces*
the condition rather than something that merely *encounters* it. The
crash site is a crime scene, not the suspect.

### Phase 3 — Enumerate the class

Search for every member the generator could have produced:

| Search for | How |
| --- | --- |
| Same misuse pattern | Grep the call shape: every caller of the API, every reader of the field |
| Same producer | Everything downstream of the generator you found in Phase 2 |
| Copy-paste lineage | `git log -S` on the faulty snippet; siblings were pasted with it |
| Same constant / same literal | Grep the exact string or number everywhere |
| Same violated assumption | Ask: who else believes this false thing? |
| Same bug in other materials | Docs, config, migrations, tests asserting the wrong behavior |

Write the list down. A class you have not enumerated is a class you are
choosing not to fix.

### Phase 4 — Choose the kill

The extermination hierarchy, weakest to strongest:

| Level | Move | What it does |
| --- | --- | --- |
| 1. Trap | Conditional guard at the crash site | Kills exactly one rat. The colony is fine. |
| 2. Seal the hole | Validate once at the boundary where bad data enters | Kills the whole class entering this way. |
| 3. Remove the habitat | Change the data shape so the invalid state cannot exist | Kills the class, its siblings, and the ones not born yet. |

Take the highest level the change budget allows. Spraying level-1 traps
across N call sites is not diligence — it is N confessions. When the same
shape of rat keeps reappearing, that is the data structure permitting an
invalid state: escalate to level 3 (see `torvalds-doctrine` §1).

### Phase 5 — Smallest complete fix

- **Complete** = every enumerated member of the class is dead.
- **Smallest** = nothing outside the class moves.
- Delete the code your fix made obsolete — including the old traps. A
  guard that can no longer fire is a lie about the data; delete it in this
  change.

### Phase 6 — Prove the class is dead

- The regression test must fail on the old code and pass on the new. If it
  never failed, it tests nothing.
- Parametrize the test over the enumerated class when cheap: one member
  per case, the reported rat as case one.
- Re-run the Phase 3 searches and show zero remaining live hits. Paste the
  count.

### Phase 7 — Report the class

The commit or PR states, in this shape:

```text
Visible rat:   [the reported symptom]
Generator:     [the upstream producer]
Class:         [N members — where they were found]
Kill:          [level 1–3 and the mechanism]
Proof:         [test name + re-run search counts]
```

A future rat sighting in the same shape is then evidence of a *new*
generator, not a reopen of this one.

## Gate summary

| Gate | What must be true |
| --- | --- |
| Into Phase 2 | A named command, already run, red on this bug |
| Into Phase 4 | The generator is named; the class is a written list |
| Into Phase 6 | The fix level is chosen and justified against the hierarchy |
| Done | Test fails on old code, passes on new; re-run searches show zero; class reported |

## Rat tells

Signals you are looking at a population:

- "We fixed this same thing last week." — You trapped a rat and left the
  colony.
- A flaky test. — The generator produces members probabilistically.
- "One weird record." — Ask what the record's siblings look like.
- "Works locally, breaks in production." — The generator is environmental;
  enumerate what differs.
- The fix you are about to write is a guard at a call site. — Whose
  siblings did you not grep?

## Rationalization check

| Excuse | Reality |
| --- | --- |
| "The user only reported one." | The user only *saw* one. |
| "Can't reproduce the others." | The class-level test *is* the reproduction. |
| "Too risky to widen the fix." | The class is already broken in production. The risk is taken; you are only choosing whether to know about it. |
| "It's just a typo." | Grep the typo. |
| "I'll handle the others in a follow-up." | Follow-ups are where classes go to live forever. |

## When an instance fix is honest

Sometimes the class truly has one member — a literal typo in a single
string, a one-off hand-edited row. Then fixing the instance is correct,
but the honesty is conditional: you ran the Phase 3 searches, the list has
exactly one entry, and the commit says so. "It looked unique" is not the
same as "I searched and it is."

## Worked example

**Rat:** shipping-label printer crashes on `user.zip == null`.

- **Bad fix:** `if (user.zip == null) return;` at the crash site. Trap.
  The tax calculator, the address validator, and the export job all still
  receive null zips.
- **Phase 2:** the import parser accepts rows without a zip and stores
  null. That is the generator.
- **Phase 3:** grep every reader of `zip` — four consumers, six existing
  null-guards (earlier traps).
- **Phase 4:** level 2/3 — validate at the import boundary; make
  `Address.zip` non-nullable. Invalid rows are rejected with a real error
  at the door.
- **Phase 5:** the six old null-guards are deleted — they can no longer
  fire and now lie about the data.
- **Phase 6:** parametrized test feeds malformed rows through the parser;
  old code fails on every case, new code rejects each with the row number.
  Grep for `zip == null` returns zero live hits.
- **Phase 7:** commit: `zip: validate at import boundary — class was 4
  consumers; deleted 6 null-guards`.

## It's working if

- The fix lands at a generator or boundary, not at a crash site.
- You can name the class size before you write the fix.
- The regression test provably fails on the old code.
- The diff deletes at least as many guards as it adds.
- The commit message reports the class, not just the symptom.

## Where it fits

`rat-principle` is the defect-time application of `torvalds-doctrine` §1:
follow the data to the generator, fix the shape, delete the special cases.
Load that skill for the design-time half of the same discipline.
