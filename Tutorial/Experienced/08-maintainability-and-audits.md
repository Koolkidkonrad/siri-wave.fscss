# 08 — Maintainability and audits

Software doesn't rot when it's read. Waves rot when their knobs stop making sense. This lesson is the "reading your own siri-wave project after a 6-month gap" playbook.

## A mini audit checklist for any siri project

**1. Token inventory against usage.**

```fscss
exec(_log, "@arr.token-usage!.list")   /* conceptual — see note */
```

Realistically: grep your sheet for `--siri-wave-` and `--blob-`. Every token should be *read by a mixin* or *used as an override*. A "knob nothing turns" (Advanced/01 warned) is debt: future-you rewires it for a knob that was supposed to work already.

**2. Override placement.**

- Are overrides scoped to the wave that needs them, or leaked into `:root`?
- Does every `:root` override have a comment saying *what theme it belongs to*? (Inter/06's three-block organization.)
- Can a new dev tell default-from-custom at a glance? If not, annotations live outside the file — put them in.

**3. Theme smoke-tests worth scripting.**

```fscss
@media (prefers-reduced-motion: reduce) { --blob-anim: none; }
```

Test that reduce toggle **and** the dark theme **and** the state-class switches — before a refactor, not after. Waves get "fixed" by someone who kills the reduced-motion line unknowingly.

## The drift you must name

Remember Experienced/01 caught `28px` in tokens vs `24px` fallback in the mixin? That's drift — two opinions of one truth in one library. File it, fix it, or document it. Every library has a couple; the audit's job is to have *named* them so the next reader doesn't rediscover them angrily.

## The refactor contract

If you touch `siri-tokens` or a preset:

1. Run any page using the preset. No console warnings, wave renders, motion on.
2. Verify `@keyframes siri-pulse` still exists if nothing renamed it.
3. Re-run reduced-motion + the audio bridge (they touch the same `--blob-*` surface).
4. Bump version honestly (Experienced/06) if behavior changed.

That's twenty minutes of checks for a library someone else leans on.

## Documentation that earns its keep

Three files matter for a wave lib:

- **README** — 3-line install + usage, override pattern, screenshot.
- **CHANGELOG** — every bump with what changed.
- **The token table** — one markdown table of `--token` / default / "what it does". Update it *at the same commit* as the code, not next era.

If the token table lives in a comment block next to the define (like `siri-tokens` ships), it dies with the code truth — that's preferrable to a docs/ file nobody edits.

## Check your understanding

1. What's the cheapest reliable signal that a project has drifted?
2. Why update the token table in the same commit as the code?

Answer 1: a token read by no mixin and used by no override — dead knob. Answer 2: tables and code drift the instant they're committed separately; colocating forces the "did I document the change" check at the same moment.

Next: [09 — The FSCSS ecosystem](09-the-fscss-ecosystem.md).
