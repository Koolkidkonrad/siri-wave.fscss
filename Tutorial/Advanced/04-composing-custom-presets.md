# 04 — Composing custom presets

`siri-wave-preset` is a define that calls five other defines. Everything you've built so far can be glued the same way — that's the master pattern of the whole course, and it's an *FSCSS + design* trick, not a siri one.

## The preset you already know

```fscss
@define siri-wave-preset(st:.siri-wave){`
  @siri-base(@use(st))
  @siri-wave(@use(st))
  @siri-line(@use(st) .siri-line)
  @siri-blob(@use(st) .blob)
  @siri-blob-colors(@use(st) .blob)
`}
```

Five calls, all scoped to the same `st`, layered inside-out. That last line — "**five calls, one anchor**" — is the entire recipe.

## Build one that adds your variant-plus

Take `siri-wave-preset` and grow it with the mixin from Advanced/02:

```fscss
@define my-siri-preset(st:.siri-wave){`
  @siri-base(@use(st))
  @siri-wave(@use(st))
  @siri-line(@use(st) .siri-line)
  @siri-blob(@use(st) .blob)
  @siri-blob-colors(@use(st) .blob)
  @siri-blob-variant-plus(@use(st) .blob, magenta)
`}
```

Now every `my-siri-preset` site gets the stock wave *plus* magenta's custom animation-name handling. Call it exactly like the stock one:

```fscss
@my-siri-preset(.hero-wave)
```

One line, whole enhanced wave.

## A preset that also adds an overlay

Presets can emit more than mixin calls — plain CSS too:

```fscss
@define branded-wave(st:.brand-wave){`
  @siri-base(@use(st))
  @siri-wave(@use(st))
  @siri-line(@use(st) .siri-line)
  @siri-blob(@use(st) .blob)
  @siri-blob-colors(@use(st) .blob)

  @use(st)::after{
    content: "";
    position: absolute;
    inset: 0;
    border-radius: inherit;
    box-shadow: inset 0 0 40px rgba(0,0,0,0.55);
  }
`}
```

The `::after` vignette rides along in every `@branded-wave()` call. Markup? Unchanged. That's a preset carrying a *design opinion* — massively repeated value in one reusable block.

## Composing presets that take data

Presets can pass parameters through like any define:

```fscss
@define energy-wave(st, spike){`
  @siri-wave-preset(@use(st))
  @use(st){
    --blob-magenta-height: @use(spike);
  }
`}

@energy-wave(.hero-wave, 140px)
```

`.hero-wave` = full wave with magenta at 140px. Parameterize everything people will brand differently; leave the rest alone.

## The discipline that keeps presets composable

1. **One anchor param.** Let every call derive from a single `st`. You can read something like `.siri-line` off it anywhere.
2. **Defaults that work bare:** `st:.siri-wave`.
3. **Delegate, then extend.** Call the stock pieces first; layer your additions after — later cascade wins for free.
4. **Don't hide semantics in a preset.** If a preset always *should* have an aria-hidden container or an offscreen label, put that in the docs or the markup, not buried in CSS.

## Check your understanding

1. What does `@energy-wave(.hero-wave, 140px)` expand to conceptually?
2. Why "delegate then extend" rather than re-roll the base?

Answer 1: full preset scoped to `.hero-wave`, plus one token override — same pattern as every preset, parameterized by data. Answer 2: the base stays one source of truth if the library evolves; your delta is self-describing JSON-ish, not a re-implementation.

Next: [05 — `$` variables and `@fun` stores](05-dollar-vars-and-fun-stores.md).
