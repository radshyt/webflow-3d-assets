# Dither hero — Webflow setup

Three code blocks plus a model file. Total weight on the wire is about
3.4 MB, nearly all of it the model, loaded asynchronously after paint.

## Install

**1. Host the model.** Webflow's asset panel won't accept `.glb`. Put
`dyno.glb` on GitHub Pages, jsDelivr, Cloudinary or any host that sends
permissive CORS headers, then copy the URL.

**2. HTML** — drop an Embed element on the page, paste
`webflow-1-embed.html`, and set `data-model` to your URL.

**3. CSS** — Page Settings → Custom Code → Inside head tag. Paste
`webflow-2-styles.css` as-is; the style tags are already in the file.

**4. JS** — Page Settings → Custom Code → Before body tag. Paste
`webflow-3-script.js` as-is; the module script tags are already in the
file.

Publish. Custom code doesn't run in the Designer canvas — use Preview or
the published site.

## Editing the phrase

Change `data-text` on the embed. `|` breaks lines:

```html
data-text="Rad Shyt|Labs"
```

At runtime: `ditherHero.setText('New|Phrase')`.

## Typography

`textFamily` defaults to `'auto'`, which reads `--font--primary-family`
off the stage element, so the headline follows your site's type without
being hardcoded. The canvas redraws once webfonts finish loading, so it
won't bake in the fallback. To override, set `data-font` on the embed or
pass `textFamily` directly.

## Tuning

Everything lives in the `CONFIG` block at the top of the JS. Change a
value there for a permanent edit, or from the console to try things live:

```js
ditherHero.set({ rayIntensity: 2.6, spinSpeed: 0.4 });
```

| What you want to change | Setting | Default |
|---|---|---|
| Resting spin | `spinSpeed` (rad/sec) | `0.15` |
| Scroll spin-up | `scrollBoost` (rad/sec per px) | `0.020` |
| Scroll spin ceiling | `scrollBoostMax` | `7.0` |
| How long it coasts | `scrollDamping` (higher = longer) | `0.955` |
| Slow vertical drift | `wobbleAmount`, `wobbleSpeed` | `0.10`, `0.30` |
| Cursor response | `mouseStrength` (mouse only) | `0.80` |
| Cursor weight/lag | `mouseEase` (lower = heavier) | `0.055` |
| Slide toward cursor | `mouseParallax` | `0.05` |
| Object size in frame | `fitHeight` (frame is ~2.9 tall) | `1.90` |
| Resting angle | `baseRotationX`, `baseRotationY` | `0` |
| Figure beam brightness | `rayIntensity` | `1.10` |
| Where a beam saturates | `rayGain` (higher = fatter) | `6.50` |
| Blackness between beams | `rayContrast` (>1 crushes haze) | `1.45` |
| Beam dither amount | `rayJitter` (0 bands, 1 mottles) | `0.45` |
| Letter beam brightness | `textRayIntensity` | `1.30` |
| Letter beam saturation | `textRayGain` | `7.00` |
| Damp letter beams at centre | `textRayInner` | `0.26` |
| Figure blocks its own beams | `rayOcclusion` | `1.00` |
| Beams swing off the cursor | `rayCursorShift` (0 = pinned) | `0.00` |
| Hard cap on beam brightness | `rayCeiling` (never raise above 1) | `1.00` |
| Glow through the letters | `textLightIntensity` | `1.00` |
| Letter emission falloff | `textLightFalloff` (2.0 = flat) | `2.00` |
| How far beams reach | `rayReach` | `0.86` |
| Beam length / falloff | `rayDensity`, `rayDecay` | `1.00`, `0.990` |
| Size of the backlight | `lightCoreSize` (see note) | `0.240` |
| Headline width | `textFitWidth` (of .dyno_3d inline width) | `0.90` |
| Central glow spread | `lightHaloSize`, `lightHaloGain` | `0.55`, `0.60` |
| Light position | `lightCenterX/Y` (0–1) | `0.5` |
| Chrome -> glass | `glass` (0 = chrome, 1 = clear) | `0.00` |
| Refraction strength | `refractionScale` | `0.80` |
| Thickness at the rim | `refractionEdge` | `0.30` |
| Centre-to-rim curve | `refractionCurve` | `1.60` |
| Refraction index | `glassIor` | `1.52` |
| Light through the glass | `glassLightBleed` | `0.45` |
| Chrome sharpness | `roughness` (lower = mirror) | `0.13` |
| Reflection strength | `envIntensity` | `1.05` |
| Grey steps in the dither | `ditherLevels` (2 = 1-bit) | `6` |
| Dither dot size | `ditherPixelSize` (CSS px) | `2` |
| Overall grade | `exposure`, `contrast`, `lift`, `vignette` | — |
| Tone curve hinge | `contrastPivot` | `0.45` |

Also available: `ditherHero.stop()`, `.start()`, `.destroy()`.

### Notes on the look

The beams are real volumetric light — a black silhouette of the object
occluding a bright centre, radially blurred — so the shape of the model
determines the shape of the beams. A solid mass throws broad wedges; a
shape with gaps throws thin shafts. If you want finer beams from the
dino, reduce `lightCoreSize` rather than touching the blur settings.

### How the light is built

**`lightCoreSize` must be larger than the figure's silhouette.** This is the
single most common way to lose the backlight: if the core is smaller than the
figure, the figure covers it completely, no light escapes around the edges,
and the only light left in the frame comes from the letters. At `0.140` the
backlight contributed nothing. `0.240` clears the silhouette. You can check
this at any time by setting `textRayIntensity: 0` — whatever remains is the
backlight, and if the frame goes black the core is too small.

`rayCursorShift` moves the point the beams converge on, and that point is what
the eye reads as the light source — so anything above about `0.05` looks like
the lamp itself wandering around the screen rather than the beams sweeping.
It now defaults to `0`.

There are two sources. A round backlight sits behind the figure, sized by
`lightCoreSize` so it spills around the silhouette — that's what rim-lights
the figure and pushes beams through the gaps under its jaw and between its
legs. Make it much smaller and the figure covers it completely, which kills
the effect; that was the bug in the earlier version.

Separately, the letterforms themselves emit. In the occlusion pass the
glyphs are drawn as light rather than as a mask, so the beams fan out of
the letters like stained glass. `textLightIntensity` sets how hard they
glow and `textLightFalloff` how far from centre they keep glowing.

The beams are shaped by two controls working together. `rayGain` multiplies
the ray buffer before `rayContrast` raises it to a power. Gain decides where
a beam saturates; the exponent decides how fast everything below that falls
to black. Order matters: the raw buffer peaks around 0.2, so applying the
exponent without gaining first crushes the beams along with the haze, and
gaining without the exponent floods the frame with grey. If the background
ever looks washed out, raise `rayContrast` before touching anything else.

The figure is a solid mass, so it throws fewer and broader shafts than a
shape with gaps would. Lowering `lightCoreSize` tightens them; raising
`rayDecay` toward 1.0 lets them travel further before dying.

### Glass

`glass` fades the figure from solid chrome to clear glass. Because metal
physically cannot transmit light, the slider fades `metalness` out as it
fades transmission in — so don't set `metalness` yourself while `glass`
is above zero, and keep it in the 0–1 range regardless.

`refractionScale` is how hard the headline bends as it passes through the
body. Around `0.5` reads as thick glass; past `1.5` the letters smear into
abstraction. `glassIor` shifts the character of the bend (1.33 water,
1.52 glass, 2.4 diamond).

`glassLightBleed` controls how much of the beam light passes through the
figure rather than being stopped by it. At `0` a glass dino still throws
a hard shadow, which looks wrong; the default lets roughly half through,
so the beams continue below the figure.

Glass adds one extra render pass per frame, but only the headline plane
is opaque, so that pass is cheap.

### A note on contrast

The tone curve is applied to the render, and the beams are added *after*
it. That ordering matters: with the beams added first, a high `contrast`
value crushed them out of the frame entirely. `contrastPivot` sets where
the curve hinges — lower values keep the shadows open.

## Sizing and resizing

The headline is fitted to width. The widest line is measured and the font
size scaled by the resulting ratio until it fills exactly `textFitWidth` of
the **stage** — the element this canvas maps onto 1:1 — using the same
ratio-correction loop as a DOM fit-to-width helper, five passes with a 0.002
tolerance.

It deliberately does *not* measure a separate wrapper. If `.dyno_3d` is wider
than the stage for any reason — a full-bleed wrapper, the stage sitting inside
a padded column — the target width exceeds the canvas and the glyphs are drawn
off both edges and clipped into fragments. Measured against the stage that
cannot happen. Correcting by ratio rather than shrinking once is what
stops the outer glyphs clipping, since letter-spacing means width does not
scale perfectly linearly with font size.

Only the container's inline width is ever read. Viewport height is not
consulted anywhere in the type path, so the phrase scales purely with width
and its proportions never change. Redraws are coalesced into one animation
frame, and it refits once webfonts settle.

If you move the stage into a different wrapper, keep the `.dyno_3d` class
and its `container-type` on the parent, or the type falls back to the
stage's own width.

On touch devices the stage height is pinned in pixels at load and only
revisited when the width actually changes. A mobile browser collapsing or
expanding its toolbar changes the viewport height mid-scroll, and anything
sized off that height jumps; pinning means the toolbar can come and go
without the layout moving at all. Set `lockHeightOnTouch: false` to opt out.

## Two independent beam sources

The backlight behind the figure and the light through the letters are
carried in separate channels of the same buffer and blurred in the same
loop, so each has its own controls at no extra cost. `rayIntensity` and
`rayGain` drive the figure's beams; `textRayIntensity` and `textRayGain`
drive the letters'.

Beams are composited over the whole frame, so they used to draw on top of
the figure — the radial blur samples past the silhouette toward the centre
and picks up light that should have been blocked. The figure now writes a
stencil into the same buffer and the beams are masked by it. `rayOcclusion`
at `1.00` means nothing shows through; lower it if you want the glass to
leak.

Both sources are screened together rather than summed: `1 - (1-a)(1-b)`.
Summing and clipping meant whichever source saturated first swallowed the
other, which is why the backlight disappeared as soon as the letter beams
got bright. Screening is bounded by construction, so both always contribute.

A radial blur piles every sample onto its convergence point, so the letter
beams used to stack into a hot blob exactly where the figure sits — raising
`textRayIntensity` brightened the middle instead of the letters.
`textRayInner` damps them near the centre, so they stay thin there and open
out toward the edges.

`textLightFalloff` at `2.00` is effectively flat, so every letterform emits
equally. Lower it only if you want the outer words to dim.

The ray march uses an ordered offset rather than a random one. Random
jitter hid the low-resolution banding but sprayed grain through the
gradient, which the dither then amplified into stray dark dots inside the
bright cores and light dots out in the black.

## Motion

The figure spins slowly at `spinSpeed`. Scrolling spins it up — scroll
velocity is added to its angular velocity, then decays back to rest at
`scrollDamping`, so it winds up and coasts down rather than snapping.
This works the same on desktop and mobile.

Only the blur centre follows the cursor, by `rayCursorShift`, so the beams
sweep as you move across the screen. The lamp itself stays pinned to
`lightCenterX/Y` and never wanders or grows — it is a fixed backlight.
Set the shift to `0` to lock the beams too.

Two things keep the light from ever flooding the frame. Samples that walk
off the ray buffer contribute nothing: the texture clamps at its edge, so
without that guard every sample past the border returns the same edge texel,
and once that texel is bright the sum runs away. And `rayCeiling` caps what
the beams may add, because `rayIntensity` scales a *saturated* beam — at 5.00
a saturated region would otherwise land five times past white.

Pointer steering is mouse and pen only; touch pointer events are ignored.
Touch drags competed with page scrolling and read as jumpy, so on phones
scroll is the whole interaction.

`prefers-reduced-motion` stops the spin, the scroll response and the
pointer response.

## Mobile

The effect runs on phones. Devices matching `(hover: none)` or
`(pointer: coarse)` automatically drop to a 1.25 pixel-ratio cap, 16 ray
samples and a 0.22 occlusion buffer.

## Performance levers, in order of effect

Each of these is independent. Work down the list until the scroll is clean.

1. **`renderScale`** (0.85). The beauty pass renders at this fraction of screen
   size; the dither is applied afterwards at full resolution, so the grid stays
   crisp and the softness underneath is close to invisible. 0.7 still looks
   fine and cuts the most expensive pass to half its pixels.
2. **`maxFps`** (45). The spin is slow enough that 45 is indistinguishable from
   60, and the skipped frames are main-thread time handed back to Lenis.
   Setting 30 is very noticeable in cost and barely noticeable to look at.
3. **`maxPixelRatio`** (1.75). On a 2x display this is the difference between
   rendering 1.75x and 1.25x the CSS pixels in each direction — roughly double
   the fill rate. This is the biggest single number in the file.
4. **`glass`**. Any value above 0 makes three.js run an extra transmission
   render plus a mipmap chain every frame. To find out what it costs you, set
   `ditherHero.set({glass: 0})` in the console and watch the scroll. If that
   is the bulk of it, the honest options are a lower value or no glass.
5. **`rayUpdateEvery`** (2). The occlusion pass pushes the whole model through
   a second geometry pass. 3 is still smooth for a slow spin.
6. **The model.** `dyno-lite.glb` is the same shape at 66k triangles instead of
   132k and 1.7 MB instead of 3.3 MB. It halves the geometry cost of *both*
   passes and the download. The trade is mild faceting in the reflections,
   which the dither partly hides.

Note that the two geometry passes cost the same regardless of `renderScale`
or `occlusionScale` — vertex work does not shrink with resolution. Only the
model itself and `rayUpdateEvery` touch that half of the budget.

## Performance

Four passes per frame: occlusion at 30% resolution, a 24-tap radial blur
at that same size, the beauty pass, then the grade-and-dither composite.
Only the beauty pass runs at full resolution.

- Scroll position is sampled inside the render loop, not from a scroll
  event. Smooth-scroll libraries such as Lenis animate scroll on their own
  rAF and fire events every frame; sampling once per rendered frame stops the
  two loops fighting. If `window.lenis` is exposed, its `animatedScroll` is
  read directly.
- When frames run long the beam passes are skipped more aggressively, so a
  busy main thread degrades the beams rather than the scroll.
- The occlusion and beam passes run every `rayUpdateEvery` frames (default
  2). Those two passes push the whole model through a second geometry pass,
  which is the expensive part of the frame; the beams change slowly enough
  that halving their rate frees the main thread during scrolling with no
  visible difference.
- Default pixel ratio cap is 1.50.
- Rendering pauses when the hero scrolls out of view and when the tab is
  hidden.
- Pixel ratio is capped at 1.75, so 4K displays don't render 4× the pixels.
- The environment map is drawn procedurally on a canvas — no texture
  downloads.
- Antialiasing is off; the dither makes it invisible anyway.

The model is a single mesh, so it's one draw call.

## The model

Your original was 38 MB and 1,318,980 triangles — a Substance 3D Stager
export. That would have dominated your load time.

`dyno.glb` is the same model decimated to 131,898 triangles, welded,
joined to one mesh, recentred on its bounding box, and compressed with
meshopt. 3.3 MB.

I chose meshopt over Draco deliberately: the decoder is about 25 KB and
decodes in milliseconds, where Draco's is roughly 200 KB and much slower.
For a hero that should feel instant, decode time matters more than the
last megabyte.

`dyno-lite.glb` is the same pipeline at 65,948 triangles / 1.7 MB. I
didn't ship it as the default because chrome amplifies faceting — the
decimation artefacts that are invisible on a matte render show up as
streaking in mirror reflections. Use it if load time beats fidelity.

## If nothing renders

- Check the browser console. A CORS error on the `.glb` is the usual cause.
- Confirm the JS is wrapped in `<script type="module">`.
- Confirm the importmap (block 1) appears before the script (block 3).
- Custom code only runs on the published site, not the Designer canvas.
