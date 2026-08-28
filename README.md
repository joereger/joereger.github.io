# joereger.com

Personal landing page for Joe Reger. Deliberately a single `index.html` with
zero dependencies — no framework, no build step, no external requests. Vanilla
HTML/CSS/JS, served by GitHub Pages.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The entire site: markup, styles, and script inline |
| `CNAME` | Custom domain for GitHub Pages (`joereger.com`) |
| `JoeReger-LowPoly-Black-T-shirt-v02.png` | Base avatar frame (always visible) |
| `JoeReger-LowPoly.png` | Suit avatar frame (shown only during flickers) |
| `x.svg`, `linkedin.svg`, `instagram.svg` | Social icons (16px row) |
| `.gitignore` | Ignores local cruft |

## How it works

### Layout & centering

The avatar sits in `.avatar-float`, a full-viewport overlay
(`position: absolute; inset: 0; display: grid; place-items: center`).
Grid centering is used instead of `top/left: 50% + translate: -50%` because an
absolutely-positioned element with no explicit width gets a shrink-to-fit box
clamped by the space right of `left: 50%`; on portrait phones the large intro
avatar exceeds that, so the translate centered the wrong box and the avatar
drifted left after the shrink (visible on mobile Chrome). The overlay has
`pointer-events: none` so it never blocks the social links beneath it.

The social icon row (`.links`) and tagline (`.tagline`) are centered with
`left: 0; right: 0; margin-inline: auto` plus a width (`max-content` /
`25vmin`), avoiding the same translate pitfall.

### Intro shrink

The avatar starts at `min(70vmin, 600px)`. After 3 seconds a `setTimeout` adds
the `small` class to `<body>`, shrinking it to `25vmin` via a CSS `width`
transition (`0.8s cubic-bezier(0.65, 0, 0.35, 1)`). `body.small` also fades in
the icon row and tagline (staggered transition delays).

### Float & sway

Two gentle infinite keyframe animations: `float` (6s, ±10px translateY on
`.avatar-float`) and `sway` (7s, ±3° rotate on `.avatar`).

### "1980s broken transmission" flicker

Two stacked `<img>`s fill the circle: the t-shirt base and the suit layer
(`opacity: 0` by default; toggled with class `on`, no transition = hard cuts).
The JS scheduler:

1. Starts only after the 3s intro, and only if
   `prefers-reduced-motion: reduce` is not set.
2. Waits a random 3–9s, then runs a flicker of total duration 0.1–0.8s split
   into 1–4 segments with random-weighted, uneven durations.
3. Segments alternate frames (no consecutive repeats; a single-segment flicker
   is always the suit).
4. During the flicker, `.glitch` adds a brightness/contrast blip and each
   segment randomly applies ±2px horizontal jitter (`jitter-l` / `jitter-r`)
   or none.
5. Ends back on the base frame and schedules the next flicker.

## Tuning knobs

CSS (in `<style>`):

- Background: `#0d0d0d` on `body`
- Intro avatar size: `min(70vmin, 600px)`; shrunk size: `25vmin`
  (`body.small .avatar`) — the `12.5vmin` in `.links` / `.tagline` `top`
  offsets is half that, so change them together
- Shrink transition: `width 0.8s cubic-bezier(0.65, 0, 0.35, 1)`
- Float/sway: keyframe durations and amplitudes
- Glitch garnish: `filter: brightness(1.15) contrast(1.1)`, jitter `±2px`
- Icons: 16px, gap 20px; tagline: 11px `#5a5a5a`

JS (in `<script>`):

- Intro delay: `3000` ms
- Flicker cadence: `rand(3000, 9000)` ms between flickers
- Flicker length: `rand(100, 800)` ms total, `1 + floor(random()*4)` segments
- Segment weight range: `rand(0.5, 1.5)`

## Deployment

GitHub Pages serves the `main` branch of `joereger/joereger.github.io` at the
apex domain via the `CNAME` file (DNS points `joereger.com` at GitHub Pages).
There is no build step: **push to `main` = deploy**. Allow ~10 minutes for the
GitHub Pages CDN cache to refresh after a push.
