# 01 — Build your own tokens

You've overridden tokens a dozen times. Now you'll *write your own token set* — the same way `siri-tokens()` does — so you understand the two ways to define one.

## The source you're copying

```fscss
@define siri-tokens(root:root){`
  @use(root){
    --siri-wave-container-width: 100%;
    /* ... */
  }
  @keyframes siri-pulse { ... }
`}
```

A `@define` declaring a parameter (`root:root`), dumping variables into a selector, shipping keyframes beside them. That's it. There's no ritual — a token mixin is just a define whose job is variables.

## Your own, cleaner version

```fscss
@define breathe-tokens(root:root){`
  :@use(root){
    --breathe-box-bg: #0a0a12;
    --breathe-box-height: 200px;
    --breathe-box-radius: 16px;
    --breathe-dot-color: #ff2ecb;
  }

  @keyframes breathe-pulse {
    0%, 100% { transform: scale(0.7); opacity: 0.5; }
    50%      { transform: scale(1.2); opacity: 1; }
  }
`}

@define breathe-dots(st){`
  @use(@use(st) .dot){
    width: 40px; height: 40px;
    border-radius: 50%;
    background: var(--breathe-dot-color, #ff2ecb);
    animation: breathe-pulse 1.6s infinite ease-in-out;
  }
`}

breathe-tokens()
breathe-dots(.stage)
```

You now maintain your own small motion system: `breathe-tokens()` for knobs + keyframes, `breathe-dots` for the shape. This is *the* siri-wave pattern applied to something new.

## The block-string path

Earlier in the course you saw defines carry backtick templates. Tokens can too — handy when the variable block is nested:

```fscss
@define breathe-tokens(root:root){
  `
  :@use(root){
    --breathe-box-bg: #0a0a12;
    --breathe-box-height: 200px;
  }
  @keyframes breathe-pulse { 0%,100%{transform:scale(.7);opacity:.5} 50%{transform:scale(1.2);opacity:1} }
  `
}
```

Same output, stringier source. Which you prefer is taste; the source and the block form compile identically.

## Three rules for token authoring

1. **Namespace everything.** `--breathe-*`, never `--bg`. You'll thank yourself at 400 afternoons from now when another module ships a `--bg`.
2. **Keep keyframes with the tokens that time them.** Same mixin, so nobody calls a motion cycle without its animation definition.
3. **Ship a matching fallback everywhere you read the token.** `var(--breathe-box-height, 200px)`. Your mixins should work even when tokens never got called — that's what makes a library demoable in three lines.

## When to break the rules

One honest exception: token *values* should live in one define (that's `siri-tokens`), but token *lifetime* is yours to manage. siri-tokens writes to `:root` so tokens are global. Set the same define to write to a scoped class and you get scoped tokens for free:

```fscss
@define breathe-tokens(s: .hero){`
  @use(@use(s)){
    --breathe-box-height: 300px;
  }
`}
```

Same mixin, different scope. The parameterization was there all along.

## Check your understanding

1. Why must a token define also carry the keyframes?
2. Your define writes `--my-box-height` into `:root`. Without calling it, a rule reads `var(--my-box-height, 120px)`. What wins?

Answer 1: keyframes can't be stored as variables — bundling them guarantees a call delivers a complete system. Answer 2: the 120px fallback — tokens unset means fallbacks, which is the whole "works without setup" story.

Next: [02 — Custom blob variants](02-custom-blob-variants.md).
