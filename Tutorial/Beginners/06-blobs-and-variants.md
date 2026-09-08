# 06 — Blobs and variants

This is where siri-wave earns its name. Two layers of tokens make a blob: the *shared* set, and the *per-color* set the loop builds.

## Shared blob tokens

Applied to the base `.blob` element:

| Token | Default | What it controls |
|---|---|---|
| `--blob-radius` | `50%` | Circle shape |
| `--blob-filter` | `blur(10px)` | The soft, glowing look |
| `--blob-blend` | `screen` | Blend mode — lets colors layer brightly |
| `--blob-opacity` | `0.9` | Blob transparency |
| `--blob-anim` | `siri-pulse 2.2s infinite ease-in-out alternate` | The animation (see lesson 07) |
| `--blob-will-change` | `transform, opacity` | Tells the browser to optimize those |

Every blob inherits these. This is the "blob-ness" of a blob — shape, softness, how it combines with its neighbors, and what motion loop it's on.

## Per-color tokens

Then each color gets its own sizing/position/timing set. Named with a `@use(color)` splice, so `magenta` yields `--blob-magenta-*`, `cyan` yields `--blob-cyan-*`, and so on:

| Token pattern | Default (magenta) | Controls |
|---|---|---|
| `--blob-<color>-width` | `78px` | Width |
| `--blob-<color>-height` | `42px` | Height — the knob you'll touch most |
| `--blob-<color>-bg` | `#c42bff` | Blob color |
| `--blob-<color>-left` | `22%` | Horizontal position across the wave |
| `--blob-<color>-delay` | `0s` | Animation delay (stagger) |
| `--blob-<color>-duration` | `1.9s` | One animation cycle |

The six colors and their default sizes:

| Color | Width | Height | bg | left | delay | duration |
|---|---|---|---|---|---|---|
| magenta | 78px | 42px | `#c42bff` | 22% | 0s | 1.9s |
| cyan | 68px | 38px | `#00c2ff` | 30% | 0.25s | 2.1s |
| green | 90px | 52px | `#30d158` | 42% | 0.45s | 2.3s |
| purple | 72px | 40px | `#af52de` | 55% | 0.15s | 2.0s |
| orange | 64px | 36px | `#ff375f` | 64% | 0.35s | 1.8s |
| blue | 70px | 40px | `#0a84ff` | 72% | 0.55s | 2.4s |

Look at the delays: 0, 0.25, 0.45, 0.15, 0.35, 0.55 — deliberately off-beat. Nothing pulses in sync. That's what makes the wave feel organic rather than like six metronomes.

## Try it: reorder the blobs

You can reposition the whole band without touching HTML:

```fscss
:root {
  --blob-green-left: 10%;
}
```

Now green sits on the left edge, swinging away from its 42% default. No HTML change.

## Try it: turn one blob up to eleven

```fscss
:root {
  --blob-magenta-height: 90px;
  --blob-magenta-duration: 1.2s;
}
```

Magenta becomes the dominant spike, fast. This is the exact knob the Advanced-level live-audio trick plays with: height = signal strength.

## The rule that ties it together

```fscss
@siri-blob-variant(st, color) {`
  @use(st) {
    width: var(--blob-@use(color)-width, 70px);
    height: var(--blob-@use(color)-height, 40px);
    /* ... */
  }
`}
```

One mixin, parameterized by a *color name string*, reads six variables built from that name. It never knows what "magenta" means; it just assembles `--blob-magenta-*`. Same trick powers the whole library.

## Check your understanding

1. Why do the delays look chaotic instead of 0, .1, .2, .3...?
2. What happens if you set `--blob-green-width: 140px`?

Answer 1: organic rhythm. Answer 2: green just gets wider — that token drives width alone, and the animation keeps running.

Next: [07 — The pulse animation](07-the-pulse-animation.md).
