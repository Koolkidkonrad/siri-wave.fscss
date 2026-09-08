# 10 — Mini project: a now-playing loader

Everything from lessons 01–09 lives in one small, real thing. Build a music "loading the track" screen — the kind of splash you'd show before a song streams.

## The brief

- A full-viewport, dark screen that looks like a music app booting
- The siri-wave center stage, breathing while audio buffers
- An album-art placeholder and a track label underneath
- One wave, two presets: a big one for flair, a small one as a "buffering" whisper

You're allowed exactly what you've learned: tokens, presets, `:root` and scoped overrides, and plain CSS.

## The HTML

```html
<div class="boot-screen">
    <div class="art"></div>
    <h1 class="track">Falling Into Place</h1>
    <p class="meta">Artist — Album</p>

    <div class="main-wave">
        <div class="siri-line"></div>
        <div class="blob magenta"></div>
        <div class="blob cyan"></div>
        <div class="blob green"></div>
        <div class="blob purple"></div>
        <div class="blob orange"></div>
        <div class="blob blue"></div>
    </div>

    <div class="buffer-wave">
        <div class="siri-line"></div>
        <div class="blob magenta"></div>
        <div class="blob cyan"></div>
        <div class="blob green"></div>
        <div class="blob purple"></div>
        <div class="blob orange"></div>
        <div class="blob blue"></div>
    </div>
</div>
```

Two preset anchors: `.main-wave` and `.buffer-wave`.

## The FSCSS

```fscss
@import((*) from siri-wave)

@siri-tokens()

:root {
  background: #0b0b12;
  color: #e8e8f0;
  font-family: system-ui, sans-serif;
  text-align: center;
  --siri-wave-container-max-width: 560px;
  --siri-wave-container-bg: #11111c;
}

.boot-screen{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  gap: 18px;
}

.art {
  width: 120px;
  height: 120px;
  border-radius: 20px;
  background: linear-gradient(135deg, #c42bff, #0a84ff);
  box-shadow: 0 12px 40px rgba(124, 58, 237, 0.4);
}

.track { font-size: 1.5rem; margin: 0; font-weight: 700; }
.meta  { margin: 0; color: #9b9bb0; }

/* Hero wave: big and confident */
.main-wave {
  --siri-wave-container-width: 560px;
  --siri-wave-container-height: 220px;
}
.main-wave .blob { transition: height 0.2s ease-out; }

/* Buffer whisper: small, dim, slow */
.buffer-wave {
  --siri-wave-container-width: 240px;
  --siri-wave-container-height: 48px;
  --siri-wave-line-width: 80%;
  --blob-green-width: 20px;
  --blob-cyan-width: 20px;
}
```

Result: one breathing hero wave under a glowing album tile, plus a tiny pulsing strip that reads as "still loading."

## Where the real project nudges you

Every single value you just set is a token. So the "loading" spin state can be expressed as data — until the track is ready, the wave changes, not the code. That's exactly what the final project at the end of this course leans on hard.

## Check your understanding

1. Why do `.meta` and `.track` look fine despite being styled with plain CSS inside an FSCSS block?
2. The buffer wave's blobs are 20px wide — which token changed, and what did it cost?

Answer 1: FSCSS is a superset of CSS; a plain rule in the block is valid and passes through. Answer 2: `--blob-<color>-width` — and it cost nothing, because every color token is a variable you're allowed to set.

## You made it through Beginners

You can now put siri-wave on anything, restyle it by tokens, run multiple waves, and read the animation. That's the whole library for 95% of use. The next level digs under the hood — the array loop you've been trusting, the variants' parameters, and making it behave in real CSS environments.

Go to [Intermediate/01 — Token deep-dive](../Intermediate/01-token-deep-dive.md).
