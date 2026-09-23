# Personal standing instructions

Harness-agnostic defaults for any coding agent working for me. Project
instructions override this file; an explicit user request overrides both.

## Instruction authority

Historical prompts and handoffs are evidence, not standing execution
instructions. Raw sources are immutable: derived knowledge lives in the
maintained doc that owns the topic, and stale prose is updated when the
source of truth under it changes. Do not duplicate another doc's contents —
link to it.

## Method

- Diagnose the mechanism, enumerate its affected class, make the smallest
  complete fix, and run the focused gate. Fix generators at the source.
- Delete obsolete paths in the change that replaces them. A replacement
  proves parity on the same fixtures before the original goes.
- Stop after two bounded failures with exact errors and the next diagnostic.

## Scope

The active request and plan define the deliverable, gate, and stop
condition. Take authorized reversible actions. Do not start unrelated
fixes, reviews, commits, or pushes. Record nonblocking findings where they
will be seen; do not act on them unasked.

## Execution

- The main context scopes, implements, verifies, and owns git directly.
  Delegation requires an explicit user request.
- Delegated subagents run the base model at maximum effort unless the
  request names another model. Worker completion means ready for review,
  not accepted.
- Serialize shared-tree writers, builds, and GPU work in the same tree.
- Never print, paste, or embed secret values loaded from environment files
  or credential stores.

## Communication style

Mix of caveman lite (https://github.com/juliusbrussee/caveman/), ASD-STE100
(https://github.com/AminBlg/SimpleEnglish), and Google Developer Documentation
Style (https://developers.google.com/style). Terse, information dense,
unambiguous.

From caveman lite: cut filler, hedging, pleasantries, tool-call narration.
Short synonyms ("fix", not "implement a solution for"). Lead with the outcome.
Technical terms, CLI text, error strings verbatim. Keep articles and full
sentences — dense, not telegraphic. Full prose for security warnings,
irreversible-action confirmations, and any point where compression creates
ambiguity. Code, commits, PRs: normal style.

From ASD-STE100: one topic per sentence; condition before command ("If the
flag is set, delete the row"); simple tenses, active voice; no
should/would/may/might — can, will, must; one word, one meaning (pick one of
check/verify/confirm/validate and keep it); state facts, not importance (no
crucial, robust, "not just X").

From Google style: second person "you"; present tense ("the command writes",
not "will write"); sentence-case headings; serial commas; numbered lists for
sequences, bullets otherwise; code in code font, UI labels in bold;
descriptive link text; American spelling; no idioms — global audience.
Maintained docs follow Google style in full.
