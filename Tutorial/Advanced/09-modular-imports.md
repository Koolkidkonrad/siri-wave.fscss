# 09 — Modular imports

Grown projects stop being "one style block that has everything." siri-wave is the library; your overrides, your preset variations, your state themes should be *your* files, imported in order.

## Split by what changes

A healthy siri-wave project file tree:

```
theme.fscss      →  tokens, palettes, @fun stores, @event themes
my-siri.fscss    →  custom variants and presets (Advanced/02-04)
states.fscss     →  state classes styling tokens (Inter/10 style)
main.fscss       →  imports the above + siri-wave, then the app
```

The FSCSS docs even show the split pattern:

```fscss
@import(exec(_theme.fscss))
@import(exec(_layout.fscss))
```

`exec()` here is doing the work of *pulling a local module's defines into scope* — the docs' shortcut for "include this file's reusable blocks." You follow the same shape: theme file first (tokens exist before anyone reads them), then the mixins, then the calls.

## Importing siri selectively

Wildcard imports are fine (`@import((*) from siri-wave)`), but project hygiene sometimes wants just the pieces:

```fscss
@import((
  siri-tokens,
  siri-wave-preset
) from siri-wave)
```

```fscss
@import((siri-wave-preset as sw-preset) from siri-wave)
```

Aliasing avoids clashes if two libraries export overlapping names — the `as` form from the import guide. Named imports also make your dependencies *visible*: a reader sees at a glance that this project only uses tokens + preset.

## Building your own multi-file library

Publish overrides as a module consumers import once:

```fscss
/* my-siri.fscss */
@define my-tokens(root:root){`
  @use(root){
    --siri-wave-container-bg: #0a0a12;
    --blob-green-height: 120px;
  }
`}

@define my-wave(st:.siri-wave){`
  @siri-wave-preset(@use(st))
  @my-tokens(@use(st))
`}
```

Market it: "drop `@import((*) from "my-siri.fscss")`, then `@my-tokens()` + `@my-wave(.wave)`." You've become a library inside a library.

## Order discipline — the #1 real-world bug

- Tokens / stores must import **before** anything that reads them.
- Keyframes (they ride inside `siri-tokens`) must exist **before** the animation references them.
- Overrides must come **after** the library's defaults.

Get the order wrong and you'll see fallback values, dead animations, and "why is my override doing nothing" — all because the layer order silently flipped. When a mysteriously-undone override appears, reorder imports first, blame yourself second.

## Check your understanding

1. Why does `@import(exec(_theme.fscss))` lead your import list?
2. Name two things a `selective/alias` import buys you over `(*)`.

Answer 1: theme defines must exist before anything reads token values; import order is compile order. Answer 2: dependency visibility (what this project actually uses) and clash-avoidance via `as`.

Next: [10 — Project: the live voice visualizer](10-project-voice-visualizer.md).
