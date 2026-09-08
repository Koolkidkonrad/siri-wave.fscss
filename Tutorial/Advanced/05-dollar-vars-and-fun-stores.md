# 05 — `$` variables and `@fun` stores

Two FSCSS features make siri-wave projects pleasant at scale: compile-time `$` variables and named `@fun` stores. They're different tools — know which one you're reaching for.

## `$` variables: compile time, then gone

```fscss
$waveHeight: 220px;
$blobTop: 42%;
```

Referenced with `!` to evaluate where you use them:

```fscss
:root {
  --siri-wave-container-height: $waveHeight!;
  --blob-green-left: $blobTop!;
  --blob-cyan-left: num($blobTop! - 6)%;
}
```

`num(...)` does arithmetic; units attach after the close paren. When this compiles, `$` vanishes — it's not a CSS variable, it's a build-time value baked in. Use `$` for *things that change during development* and `--` tokens for *things that change at runtime or per component*.

Why both? Because a `$` change recompiles everything instantly, and a `--` change can be retargeted — per wave, per scope, per state class. The strongest siri-wave setups use `$` for the constants that define the system and `--` for the knobs that vary.

## `@fun` stores: named property blocks

`@fun` holds *declarations*, referenced with a dot:

```fscss
@fun(palette){
  magenta: #ff2ecb;
  cyan:    #22d3ee;
  green:   #4ade80;
}

:root {
  --blob-magenta-bg: @fun.palette.magenta.value;
  --blob-cyan-bg:    @fun.palette.cyan.value;
  --blob-green-bg:   @fun.palette.green.value;
}
```

`.value` pulls just the value (handy for dropping inside `linear-gradient(...)` or such). `@fun` is the FSCSS-native *design-token* store: one place your palette lives, referenced anywhere.

## Using a store to scale a palette out

Say the marketing site needs ten waves in ten accent colors. Define once:

```fscss
@fun(accents){
  a: #3b82f6;
  b: #8b5cf6;
  c: #06b6d4;
  d: #10b981;
  e: #f59e0b;
  f: #ef4444;
}
```

Then a token publishing block you run once:

```fscss
:root {
  --blob-a-bg: @fun.accents.a.value;  /* and so on for b..f */
}
```

Six accent "blobs" in the same namespace the loop uses. Your future loop just feeds on `a`..`f` names. This is the Advanced-level realization of the Beginners lesson: *names are data, stores are the tables they come from*.

## Rules of thumb

- **`$` for compile-time constants.** Theme-wide singletons, math bases.
- **`--` tokens for runtime/scoped variation.** Anything a state or component needs to change.
- **`@fun` for named groups of declarations.** Palettes, spacing scales.
- Push color *values* into `@fun`, hold *tokens* in `--`, and let `$` stay out of the published surface entirely.

## Check your understanding

1. After compilation, does `$waveHeight` exist on the page?
2. Give one case where `--` beats `$` for a siri-wave value.

Answer 1: no — it's a build-time constant, inlined where `!` used it. Answer 2: any knob you override per class/state on a wave (e.g. `--blob-green-height` scoped to `.wave.active`).

Next: [06 — @event-powered themes](06-event-powered-themes.md).
