# 03 — Your own colors array

The stock wave has six blobs from one array. Advanced moves the goalposts: *your* wave, *your* palette, *your* count — generated, not copy-pasted.

## Why the array is the lever

Revisit the loop brain (from Inter/02): there's a **names** array (`siri-colors[...]`) and a **counter** (`count(6,1)`). The names list is the *data*; the loop is the *machinery*. To change the wave's colors, you change the data.

## A seven-blob rename

```fscss
@arr wave-names[magenta, gold, teal, lime, coral, violet, sky]
@arr wave-i[count(7,1)]

/* counter must match the names list length! */
```

Then generate the variants and their classes:

```fscss
@define wave-variants(st){`
  @arr wave-names[magenta, gold, teal, lime, coral, violet, sky]
  @arr wave-i[count(7,1)]

  @siri-blob-variant(@use(st).@arr.wave-names[@arr.wave-i[]], @arr.wave-names[@arr.wave-i[]])
@use(st){
--blob-variant-trigger: @arr.wave-i[];
}
`}
```

Wait — reading `@use(st).wave-names[1]` → `.wave.magenta`. Position 1 is still *named* magenta, so tell the array it lives at index 1. The meshing of class name and token name is what the loop sells: `.blob.gold` reads `--blob-gold-*`, and you define those tokens:

```fscss
:root {
  --blob-gold-width: 74px;  --blob-gold-height: 40px;
  --blob-gold-bg: #f2a200;  --blob-gold-left: 14%;
  --blob-gold-delay: 0s;    --blob-gold-duration: 2.0s;
  /* four more */
}
```

New wave, generated. Add `<div class="blob gold">` etc. to the HTML and you're done.

## Two-tone collision fix — unique suffixes

If a class name collides with the stock palette (you reuse `magenta` in your loop while the stock one also exists), give your names a suffix:

```fscss
@arr wave-names[magenta-x, gold-x, ...]
```

`.blob.magenta-x`, `.blob.gold-x` — zero collision with the stock `.blob.magenta`. Keep this whether you're adding to the stock wave or replacing it.

## The length trap

`count(7,1)` must equal the names list length. `count(6,1)` with seven names → the seventh never renders. Seven names with `count(6,1)` → the seventh name never gets a variant. Nothing errors, it just silently stops at the shorter one — check the compiled CSS when a blob looks unstyled.

## Numbers can be the data too

You don't have to use names. A width-only wave driven by a numbers array works with the same loop, just no `--blob-X-bg`:

```fscss
@arr widths[34px, 60px, 44px, 72px, 52px]
@arr widx[count(5,1)]

@use(.mini-wave .blob.@arr.widx[]){
  height: 32px;
  width: @arr.widths[@arr.widx[]];
}
```

`.blob.1` … `.blob.5` — but honestly, class names of `.1` are gross. Prefer names with meaning; numbers are for when the concept itself is ordinal (band n, step n).

## Check your understanding

1. What breaks if names list ≠ counter length, and does it error?
2. Why suffix reused palette names instead of just adding to the stock loop?

Answer 1: silent omission — the pair of arrays stops at the shorter one. Answer 2: selector + token-name collisions with stock palette; unique names keep both loops independent.

Next: [04 — Composing custom presets](04-composing-custom-presets.md).
