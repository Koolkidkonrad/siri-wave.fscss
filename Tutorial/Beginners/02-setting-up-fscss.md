# 02 — Setting up FSCSS

siri-wave runs on top of FSCSS. FSCSS is a CSS preprocessor that runs its mixins and turns them into plain CSS — it's available for prototyping *in the browser too*, at runtime. So no setup in testing mode: load the FSCSS runtime, then let siri-wave write your CSS for you.

## The runtime

Add this to your page's `<head>`:

```html
<script src="https://cdn.jsdelivr.net/npm/fscss@1.2.0/runtime.min.js" defer></script>
```

Two things about that tag:

1. The `defer` matters. Without it the script can run before your `<style>` block is parsed and siri-wave never compiles. You'll sit there staring at a black box wondering what's wrong. Use `defer`.
2. siri-wave requires FSCSS `>= 1.2.0`. That URL pins exactly that.

## Tell siri-wave what to import

Now write your first FSCSS line:

```fscss
@import((*) from siri-wave)
```

That grabs every public mixin (the `(*)` part) from the siri-wave library in the FSCSS registry. No URL needed, no CDN fiddling — the library is registered by name. That's the whole "remote import by name" thing and it's the cleanest setup you'll see.

> Stuck with `cannot import siri-wave`? Then the registry copy isn't reachable from where you're testing. Fallback that always works — import straight from this repo:
>
> ```fscss
> @import((*) from "https://cdn.jsdelivr.net/gh/Koolkidkonrad/siri-wave.fscss@main/siri-wave.fscss")
> ```
>
> Figure out which one works in your environment and keep it.

## A minimal page that proves it runs

Don't add the wave yet — just prove FSCSS is alive and talking to siri-wave:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>setup</title>
    <script src="https://cdn.jsdelivr.net/npm/fscss@1.2.0/runtime.min.js" defer></script>
    <style>
@import((*) from siri-wave)

siri-tokens()
/* use.... */
    </style>
</head>
<body></body>
</html>
```

If FSCSS is wired up, open DevTools and confirm the compiled CSS contains the `siri-pulse` keyframes. If you see `@keyframes siri-pulse` in there, the import worked and the tokens went in. If you don't, the import failed — check the console message and switch to the jsDelivr fallback above.

## package.json (optional but do it anyway)

If you'll ever use FSCSS's CLI or another tool that reads library metadata, the repo's `package.json` declares how siri-wave wants to be imported:

```json
{
  "name": "siri-wave",
  "fscss_version": ">=1.2.0",
  "file": {
    "remote": "siri-wave.fscss",
    "usage": {
      "directive": "siri-*",
      "helpers": ["siri-tokens(root:root)", "siri-base(st:.siri-wave)", "..."]
    }
  }
}
```

You don't need to type that today. It exists so the ecosystem knows: name is `siri-wave`, remote file is `siri-wave.fscss`, and everything that starts with `siri-` is fair game.

## Recap

1. Include the runtime with `defer`.
2. `@import((*) from siri-wave)` in your stylesheet.
3. Verify `@keyframes siri-pulse` exists in compiled CSS.

Setup done. Now the fun part — [03 — Your first siri-wave](03-your-first-siri-wave.md).
