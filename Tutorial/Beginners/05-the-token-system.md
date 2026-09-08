# 05 — The token system

Here's the idea that makes siri-wave feel like a different animal from writing CSS by hand: *every visual decision is a variable.* The mixins are dumber than you think — they mostly read variables with fallbacks. When you want a different look, you change a variable, not a file.

All the tokens live in one place, `siri-tokens()`, which writes them to `:root`.

## Container tokens

| Token | Default | What it controls |
|---|---|---|
| `--siri-wave-container-width` | `100%` | Container width |
| `--siri-wave-container-max-width` | `560px` | Cap on the width |
| `--siri-wave-container-height` | `160px` | Container height |
| `--siri-wave-container-bg` | `#000` | Container background |
| `--siri-wave-container-radius` | `28px` | Corner rounding |

## Baseline line tokens

| Token | Default | What it controls |
|---|---|---|
| `--siri-wave-line-width` | `88%` | How wide the line is |
| `--siri-wave-line-height` | `1px` | Line thickness |
| `--siri-wave-line-bg` | `rgba(255,255,255,0.18)` | Line color |
| `--siri-wave-line-shadow` | `0 0 6px rgba(255,255,255,0.35)` | The glow |
| `--siri-wave-line-z` | `1` | Stacking order (above blobs) |

Two things worth noticing even at the "container" stage:

1. Every mixin reads these as `var(--siri-wave-container-width, 100%)` — *variable, then fallback*. If you never set the variable, the fallback wins. If you set the variable, your value wins. No `!important`, no quests through the code.
2. The token names are namespaced. `--siri-wave-` for the wave shell, (spoiler) `--blob-` for blobs. You can grep a stylesheet for `--siri` and see every knob in the library.

## The structure of a token call

`siri-tokens()` isn't magic — it's this, roughly:

```fscss
@define siri-tokens(root:root){`
  @use(root){
    --siri-wave-container-width: 100%;
    --siri-wave-container-max-width: 560px;
    /* ...and so on... */
  }
  @keyframes siri-pulse { ... }
`}
```

So it's a mixin that dumps variables into a scope (the default `:root`) and also drops the keyframes next to them. One call, all the defaults, page-global.

## Try it: make the shell yours

```fscss
@siri-tokens()

:root {
  --siri-wave-container-height: 220px;
  --siri-wave-container-bg: #0a0a12;
  --siri-wave-container-radius: 999px;
}
```

Your wave shell is now a 220px-tall pill with a slate background. You didn't touch the library. That override-after-tokens pattern is how you'll do almost all real customization from now on.

## Why tokens and not hardcoded values?

Because one value wants to be in five places. Radius shows up in the container and (via inherit patterns you'll see later) in layered boxes you build yourself. If you hardcode, you edit five spots. If you tokenize, you edit one. siri-wave runs on that logic end to end.

## Check your understanding

1. What wins: the fallback in a `var()`, or a value you set on `:root`?
2. Why does `siri-tokens()` run once per page instead of once per wave?

Answer 1: yours does. Answer 2: the tokens live on `:root` and CSS variables inherit down to every wave, so re-calling would just overwrite the same values for no reason.

Next: [06 — Blobs and variants](06-blobs-and-variants.md) — the good stuff.
