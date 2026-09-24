---
name: tao-method
description: Attack hard technical problems the way Terence Tao does — playful, precise, and interdisciplinary, and not done until the solution is stunningly simple. Build intuition first, poke at special cases, cheat strategically with extra assumptions and then remove them, name the exact obstruction, borrow tools from neighboring fields, and verify everything mechanically before claiming it. Use for any genuinely hard or stuck problem: tricky algorithms, math and geometry, physics or numerics, proofs, puzzling bugs with no obvious cause, design problems with competing constraints, or whenever the user asks for brilliance, first-principles thinking, or a fresh angle.
license: MIT
---

# The Tao Method

**Play like a curious amateur. Check like a paranoid professional. Stop
only at the stunningly simple answer.**

Brilliance here is not a flash of genius. It is a habit: turn the problem
over until it shows you a side you can grab, try the silly version first,
steal a tool from the next field over, and never trust an argument that
went suspiciously smoothly.

## The target: stunningly simple

A working solution is a waypoint, not the finish. The finish is the
solution so simple that it explains *why* the answer is true — the one a
reader sees and thinks "of course". Complexity in a solution usually means
you solved it in the wrong frame: the right change of variables, data
structure, invariant, or borrowed idea makes the special cases disappear.

- When the solution has many cases, ask what single idea would make them
  one case.
- When the proof or the code is long, ask what it is really using. Keep
  that; drop the scaffolding.
- Prefer the solution a smart outsider can check in a minute over the one
  only you can check in an hour.
- Simple must still be correct. A short answer that the checker rejects is
  not simple; it is wrong.

## The three modes

Tao describes three stages of understanding. Use all three, in a loop:

1. **Pre-rigorous** — intuition, pictures, analogies, guesses. Fast and
   often wrong.
2. **Rigorous** — every step justified. Slow, and it catches the wrong
   guesses.
3. **Post-rigorous** — intuition again, now disciplined by the rigor. You
   can say why the answer is true in one paragraph without notation.

Stalling in mode 2 turns you into a symbol-pusher. Skipping mode 2 turns
you into a confident guesser. The work is the loop.

## Procedure

Each phase has a checkpoint. Do not open the next phase until the
checkpoint is true.

1. **Guess the answer.** Write the expected result and the gut reason for
   it in plain language.
   *Checkpoint:* one sentence of expected behavior, one sentence of why.

2. **Play with small cases.** Work the tiny, the degenerate, the extreme,
   and the symmetric cases by hand or with a throwaway script. Zero, one,
   two. Empty input. The largest value. The case where two things
   coincide.
   *Checkpoint:* at least two concrete cases computed and cross-checked.

3. **Cheat strategically.** Add a convenient assumption — the data is
   sorted, the grid is uniform, the float is exact, there is no
   concurrency — and solve that toy version fast. Then write down which
   assumption did the heavy lifting. That assumption is where the real
   problem lives.
   *Checkpoint:* toy version solved; the load-bearing assumptions listed.

4. **Name the obstruction.** Say precisely why the naive approach fails —
   not "it's hard", but the mechanism: this quantity blows up, these two
   constraints fight, this information is not available at this point.
   Screen every candidate method against the obstruction before you spend
   time on it.
   *Checkpoint:* the obstruction stated as a claim you can test.

5. **Change the lens.** Look for symmetry and normalize it away. Pick the
   notation or the data structure that makes the structure visible. Then
   cross the border: ask what a neighboring field would call this problem.
   - A scheduling conflict may be graph coloring.
   - A flaky numeric drift may be a conditioning or stability problem.
   - A search over configurations may be an optimization landscape, or a
     constraint-satisfaction problem a solver can do in seconds.
   - A "weird bug" may be an information-flow problem: which value did the
     code never get to see?
   - A geometry problem may get easier in another coordinate system, a
     projection, or its dual.
   Reformulate: prove the contrapositive, aim for an intermediate claim,
   or try hard to build a counterexample — failing to build one often shows
   why none exists.
   *Checkpoint:* at least one reformulation or borrowed tool tried.

6. **Make it rigorous, skeptically.** Write the full argument or the full
   code. Any step that worked too easily gets re-derived slowly — easy
   steps are where errors hide. Remove the cheats from step 3 one at a
   time.
   *Checkpoint:* every suspiciously smooth step redone; a fresh
   counterexample search failed.

7. **Verify mechanically.** Move trust from yourself to a checker: a test
   suite, a property-based test, a brute-force oracle on small inputs, a
   type checker, a proof assistant, a measured benchmark. A claim the
   machine cannot check stays a conjecture, and you say so.
   *Checkpoint:* the checker ran and its output is in hand.

8. **Hunt for the simple version.** You have a working, verified answer.
   Now find the one that makes it obvious. Which step carries all the
   weight? Can a better frame, invariant, or data structure make the rest
   unnecessary? Rewrite until the core fits in one paragraph or one small
   function, then run the step 7 checker on the new version too.
   *Checkpoint:* the simplest version found passes the same checker, and
   you can say what was removed and why it was never needed.

9. **Explain it simply, then keep playing.** Restate the idea in one
   paragraph a smart outsider follows. Then poke it: drop a hypothesis,
   strengthen the conclusion, find where the result stops being true.
   *Checkpoint:* the one-paragraph explanation exists and matches step 1,
   or step 1 was wrong and you know why.

## Working habits

- **Ask dumb questions.** Is this hypothesis needed? Is the converse true?
  What happens at zero? The dumb question is usually the one nobody
  checked.
- **Write down partial progress,** including dead ends and why they died.
  Memory lies; notes do not.
- **Use the wastebasket.** When an approach burns its budget, stop, record
  the lesson, and switch. Sunk cost is not evidence.
- **Don't marry one hypothesis.** Keep two or three alive until a test
  kills all but one.
- **Split big problems into many small checkable pieces.** Hard problems
  fall to thousands of easy verified steps more often than to one heroic
  one.
- **Relearn the basics** of the field you are working in. The fundamentals
  hide more leverage than the frontier.

## Delegating to tools and weaker models

Hand routine sub-problems to tools or cheaper models: case enumeration,
brute-force checks, literature and doc lookups, boilerplate. Keep the
judgment: what to formalize, whether the formal statement matches the
intent, whether the result makes sense. Use a delegated result only if you
could present it and answer questions about it yourself.

## Voice

Be playful in exploration and exact in conclusions. Enjoy the problem out
loud — "what if we just pretend the grid is infinite?" — and then be
ruthless about what is actually proved. Label every claim: **checked**
(the machine confirmed it), **argued** (a full argument, not machine
checked), or **guessed** (intuition only).

## Failure modes

| Symptom | Fix |
|---|---|
| A clean derivation you did not re-check | Redo the easiest step slowly; search for a counterexample |
| One root-cause theory for an hour | Write two rivals and design a test that separates them |
| Symbol-pushing with no picture | Stop and state the idea in plain words |
| Beautiful idea, no verification | Build the smallest mechanical check and run it |
| Stuck inside one field's toolbox | Ask which other field has solved this shape before |
| It works, but it has twelve special cases | Find the frame that makes them one case |
