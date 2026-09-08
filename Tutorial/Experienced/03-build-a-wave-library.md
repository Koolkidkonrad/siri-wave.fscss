# 03 — Build a wave library

You've read siri-wave, extended it, re-guided its tokens. Now build your own from the pattern — a twin-strip "echo wave" that runs on the exact same architecture: token mixin, shape mixins, variant loop, preset.

## The design

An **echo wave**: a primary row of blobs plus a delayed, dim echo behind them. Two rows, one color list. Reuse the siri pattern end to end.

## 1. The token mixin

```fscss
@define echo-tokens(root:root){`
  @use(root){
    --echo-min-h: 24px;
    --echo-max-h: 120px;
    --echo-row-gap: 6px;
    --echo-echo-opacity: 0.3;
  }
  @keyframes echo-tail {
    0%   { transform: scaleY(0.5); opacity: 0.2; }
    40%  { transform: scaleY(1.2); opacity: 1; }
    100% { transform: scaleY(0.6); opacity: 0.3; }
  }
`}
```

Namespace `--echo-*`, keyframes bundled. Same contract as `siri-tokens`.

## 2. Shape mixins

```fscss
@define echo-frame(st:.echo-wave){`
  @use(st){
    position: relative;
    overflow: hidden;
    display: flex;
    align-items: flex-end;
    justify-content: center;
    gap: var(--echo-row-gap, 6px);
    height: var(--echo-max-h, 120px);
  }
`}

@define echo-bar(st, color){`
  @use(st){
    width: 18px;
    height: var(--echo-min-h, 24px);
    border-radius: 999px;
    background: var(--echo-@use(color)-bg, #ffffff);
    animation: echo-tail 1.6s ease-in-out infinite;
    animation-delay: var(--echo-@use(color)-delay, 0s);
  }
`}
```

Note the *token-name synthesis* poking out: `--echo-@use(color)-bg`. Same spine as `--blob-@use(color)-bg`—we're retreading siri's skeleton on purpose.

## 3. The variant loop

```fscss
@define echo-colors(st){`
  @arr echo-colors[aqua, sun, rose]
  @arr echo-colors-i[count(3,1)]

  @echo-bar(@use(st).bar-@arr.echo-colors[@arr.echo-colors-i[]], @arr.echo-colors[@arr.echo-colors-i[]])

  @use(st){
    --echo-loop-index: @arr.echo-colors-i[];
  }
`}
```

Three bars, generated. Even the decoy property is inherited from the pattern — that block is your loop engine, remember.

## 4. The rows (primary + echo via two passes)

```fscss
@define echo-rows(st){`
  @echo-colors(@use(st) .echo-row-wrap)
  @echo-colors(@use(st) .echo-row-wrap .echo-row-echo)
`}
```

A second pass over a `.echo-row-echo` class gives the delayed echo — same color list, people more transparent because of the echo-keyframe's low peak opacity.

## 5. Tokens per color

```fscss
@use(root){
  --echo-aqua-bg: #22d3ee;  --echo-aqua-delay: 0s;
  --echo-sun-bg:  #f59e0b;  --echo-sun-delay: 0.4s;
  --echo-rose-bg: #fb7185;  --echo-rose-delay: 0.8s;
}
```

## 6. The preset

```fscss
@define echo-wave-preset(st:.echo-wave){`
  @echo-frame(@use(st))
  @echo-rows(@use(st))
`}
```

## The markup

```html
<div class="echo-wave">
  <div class="echo-row-wrap">
    <div class="bar-aqua"></div>
    <div class="bar-sun"></div>
    <div class="bar-rose"></div>
  </div>
  <div class="echo-row-wrap echo-row-echo">
    <div class="bar-aqua"></div>
    <div class="bar-sun"></div>
    <div class="bar-rose"></div>
  </div>
</div>
```

## Compile check

CLI for certainty, then CSS hunt:

```
fscss echo.fscss echo.css
```

Search the output for `.bar-aqua`, `.bar-sun`, `.bar-rose` and the `echo-tail` keyframes. If all four + the `@media`-less animated rules exist, your library's skeleton works. You've built a wave sibling to siri-wave from twenty lines — this is the whole point of the course.

## Honestly, the antipattern

Do **not** fork siri-wave.fscss and change every prefix to your name. That's a rename, not a library, and you'll carry the original's quirks (34 declarations of typed colors, the loop decoy) without deciding anything. Building from the *pattern*, as here, is the maintainable move.

## Check your understanding

1. What's the single line that turns a hand-typed bar rule into the loop-generated trio?
2. Why two `echo-colors` passes instead of one huge mixin?

Answer 1: the `@echo-bar(... @arr.echo-colors[@arr.echo-colors-i[]] ...)` call inside a counter-driven block — that's the generation line. Answer 2: two passes keep the HTML roles separate (primary / echo), each generated consistently rather than six hand-written rules in one mixin.

Next: [04 — Performance and layers](04-performance-and-layers.md).
