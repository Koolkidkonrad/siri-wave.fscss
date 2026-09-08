# 10 — Project: the live voice visualizer

Your capstone for this level: a **live voice visualizer** — the mic-to-blob bridge from Advanced/07, but pushed into a polished, production-shaped feature. You'll use tokens, variants, a custom preset, `@event` theming, and the JS bridge. Everything in Advanced/01-09 shows up.

## The brief

- A full-page "listening" screen, like a voice assistant waiting for you to talk
- The siri-wave reacts to *real* mic data (as in Advanced/07)
- A **mute fallback**: before the mic grants, the wave idles with a slow simulated pulse (no dead black box)
- A **quiet threshold**: long silence turns the wave dim and slow — "nothing heard"
- `prefers-reduced-motion` turns everything into a static, calm state
- Files split as per Advanced/09: `theme.fscss`, `preset.fscss`, `main.fscss`

## The files

**theme.fscss** — the token source of truth:

```fscss
@fun(palette){
  magenta: #ff2ecb;
  cyan:    #22d3ee;
  green:   #4ade80;
  purple:  #a78bfa;
  orange:  #fb923c;
  blue:    #38bdf8;
}

@event wave-state(mode){
  if mode: idle    { return: 0.35; }
  el-if mode: live  { return: 1; }
  el-if mode: quiet { return: 0.18; }
  el { return: 0.6; }
}

breathe-tokens()   /* from Advanced/01 — your own motion system */
```

**preset.fscss** — the component:

```fscss
@define voice-preset(st:.siri-wave){`
  @siri-wave-preset(@use(st))
  @use(st){
    --blob-opacity: @event.wave-state(idle);
    --blob-filter: blur(9px);
  }
`}
```

**main.fscss** — imports + calls:

```fscss
@import(exec(theme.fscss))
@import(exec(preset.fscss))
@import(exec(states.fscss))

@breathe-tokens()
@siri-tokens()
@voice-preset(.vox)
```

**states.fscss** — the three moods:

```fscss
.vox {
  --blob-opacity: @event.wave-state(idle);
  --blob-anim: breathe-pulse 3.2s infinite ease-in-out alternate;

  &.live {
    --blob-opacity: @event.wave-state(live);
    --blob-anim: siri-pulse 2.2s infinite ease-in-out alternate;
  }
  &.quiet {
    --blob-opacity: @event.wave-state(quiet);
    --blob-anim: siri-pulse 6s infinite ease-in-out alternate;
  }
}

@media (prefers-reduced-motion: reduce) {
  .vox { --blob-anim: none; }
}
```

## The JS that feeds it

Same bridge from Advanced/07, plus the states:

```js
const root = document.documentElement;
const vox = document.querySelector('.vox');
const colors = ['magenta','cyan','green','purple','orange','blue'];
let silence = 0;

function loop() {
  analyser.getByteFrequencyData(bins);
  let total = 0;
  for (let i = 0; i < colors.length; i++) {
    let sum = 0;
    for (let j = i * bandSize; j < (i + 1) * bandSize; j++) sum += bins[j];
    const avg = sum / bandSize;
    total += avg;
    root.style.setProperty('--blob-' + colors[i] + '-height', Math.max(8, Math.round((avg / 255) * 110)) + 'px');
  }
  // mood switching: dim the light, sleep the quiet
  if (total / colors.length < 8) {
    silence++;
    vox.classList.add('quiet');
  } else {
    silence = 0;
    vox.classList.remove('quiet');
  }
  requestAnimationFrame(loop);
}
```

## What "done" looks like

Load → wave idles gently (your own keyframes). Click to allow the mic → it comes alive, six bands dancing. Stop talking → after a few seconds it dims to the "quiet" pulse. Reduced-motion users → a static, calm wave that never animates. That's a *system*, not a demo — the state machine, the fallbacks, the accessibility, the module split are all production shapes, exactly what the final project at the end of the course will demand from you cold.

## Pull it together

You now have a component: authored tokens, generated variants, a custom preset, event-driven moods, a JS data bridge, and a files split. Next level is where you un-read the source and rebuild it — [Experienced/01 — Reading the source](../Experienced/01-reading-the-source.md).
