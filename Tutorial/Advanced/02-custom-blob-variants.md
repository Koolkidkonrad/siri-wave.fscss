# 02 — Custom blob variants

Beginners lesson 07 hid a fact: the per-color token set has no `-anim` key, so you can't give one blob a different *animation name* through tokens. This is where you stop accepting that and write a variant mixin that can.

## The stock variant, re-stated

```fscss
@define siri-blob-variant(st, color){`
  @use(st){
    width: var(--blob-@use(color)-width, 70px);
    height: var(--blob-@use(color)-height, 40px);
    background: var(--blob-@use(color)-bg, #fff);
    left: var(--blob-@use(color)-left, 50%);
    animation-delay: var(--blob-@use(color)-delay, 0s);
    animation-duration: var(--blob-@use(color)-duration, 2s);
  }
`}
```

Six keys. Read-only by design. Now we write a *superset*.

## A variant with an animation-name key

```fscss
@define siri-blob-variant-plus(st, color){`
  @use(st){
    width: var(--blob-@use(color)-width, 70px);
    height: var(--blob-@use(color)-height, 40px);
    background: var(--blob-@use(color)-bg, #fff);
    left: var(--blob-@use(color)-left, 50%);
    animation-delay: var(--blob-@use(color)-delay, 0s);
    animation-duration: var(--blob-@use(color)-duration, 2s);
    animation-name: var(--blob-@use(color)-anim-name, siri-pulse);
  }
`}
```

The `-$-$anim-name` token doesn't exist yet — but custom properties don't need to exist ahead of time. Define it on `:root` and the variant picks it up:

```fscss
:root {
  --blob-magenta-anim-name: squash-drop;
}

@keyframes squash-drop {
  0%   { transform: translateY(-24px) scaleY(0.9); opacity: 0; }
  60%  { transform: translateY(4px)  scaleY(1.3);  opacity: 1; }
  100% { transform: translateY(0)    scaleY(1);    opacity: 1; }
}
```

Now magenta drops and squashes while everyone else keeps pulsing. One new mixin, no fork of the library.

## Compositing instead of copy-paste

Rather than retype all six declarations, **call the stock mixin and add a property** — the same composition @define supports:

```fscss
@define siri-blob-variant-plus(st, color){`
  @siri-blob-variant(@use(st), @use(color))

  @use(st){
    animation-name: var(--blob-@use(color)-anim-name, siri-pulse);
    will-change: transform, opacity, top;
  }
`}
```

Stock mixin runs first (six declarations), your extra declarations land after. Same selector, later in cascade — yours win where they overlap. This "call it, then extend it" is how you'll grow every mixin you borrow.

## Keeping token regularity

Whatever keys you add, keep them flat and predictable — `--blob-<color>-<key>`. The moment you break the pattern (e.g. `--magenta-speed`), your loop can't synthesize names anymore and you're writing six hand-blocks like it's 2019.

## Check your understanding

1. Why can't you set a one-off animation name through the stock tokens?
2. What makes the composite (`call + extend`) version better than retyping the six declarations?

Answer 1: the variant only reads its defined six keys — no `-anim-name`. Answer 2: one source of truth for the base keys; your additions stay the only local delta, same logic as overriding rather than editing the library.

Next: [03 — Your own colors array](03-your-own-colors-array.md).
