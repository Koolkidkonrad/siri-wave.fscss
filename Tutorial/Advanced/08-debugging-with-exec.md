# 08 — Debugging with `exec()`

When a wave misbehaves, FSCSS gives you two old friends with new hats: `exec(_log, …)` and `exec(_warn, …)`. They print to the console at compile time — during browser runtime, that's your page's console.

## Live counters in your stylesheet

Want to see what `count()` actually produced? Ask.

```fscss
exec(_log, "count(6,1)")
/* → 1, 2, 3, 4, 5, 6 */
```

This is the fastest way to check a token or array without compiling anything else:

```fscss
exec(_log, "siri-colors list is here")
exec(_log, "@arr.wave-names!.list")
```

The `!.` is *method access mode* (docs) — `.list` joins the array into a readable string. Perfect for "is my array the length I think it is?"

## Instrumenting the loop

The classic debugging target: your generated variants (Advanced/03). You *believe* the loop fires six times; the console tells you:

```fscss
@define wave-variants(st){`
  @arr wave-names[magenta, gold, teal, lime, coral, violet, sky]
  @arr wave-i[count(7,1)]

  exec(_log, "loop: @arr.wave-i[] -> @arr.wave-names[@arr.wave-i[]]")

  @siri-blob-variant(@use(st).@arr.wave-names[@arr.wave-i[]], @arr.wave-names[@arr.wave-i[]])
@use(st){
--index: @arr.wave-i[];
}
`}
```

Run the page and read:

```
loop: 1 -> magenta
loop: 2 -> gold
...
loop: 7 -> sky
```

Seven lines. If you wrote `count(6,1)` you'd see six — and the silent seventh you suspected is now visible.

## `_warn` for "this shouldn't happen"

```fscss
@define my-preset(st){`
  exec(_warn, "my-preset called without a token call — defaults in effect")
  @siri-wave-preset(@use(st))
`}
```

Warnings don't block, but they *talk*. Handy when a consumer of your mixin skips setup. Ugly in normal output, informative when something's actually off.

## Reading the compiled CSS

`exec()` tells you what FSCSS *thought*. The compiled CSS tells you what the page *got*. DevTools → Search for `.blob.magenta`. You should see a rule whose declarations read `var(--blob-magenta-…)`. Three failures to look for there:

- No `.blob.magenta` rule → the loop never fired → array length mismatch (check `exec(_log, "@arr.wave-names!.length")`).
- Rule exists but a property is wrong → token unset → check `:root` actually carried it (the var may be missing from the token set).
- Rule exists, wave dead-silent → animation is off. Search for `@keyframes siri-pulse`. If it's not in the compiled CSS, `siri-tokens()` never ran — the classic "called the preset without the tokens" bug.

## The one-liner you'll retype forever

```fscss
exec(_log, "siri-wave compile OK")
```

Park it at the end of the import block. If that line's in the console, the import parsed; if it's not, your `@import` itself failed and nothing below matters.

## Check your understanding

1. `exec()` output appears where, at what time?
2. A wave renders static with no animation. `exec(_log, "@keyframes siri-pulse exists")`—what are you actually probing?

Answer 1: devtools console, when the stylesheet compiles (runtime mode = at page load/re-render). Answer 2: whether the token mixin ran — if the log prints, keyframes exist, and the dead animation is a selector or `--blob-anim` problem instead.

Next: [09 — Modular imports](09-modular-imports.md).
