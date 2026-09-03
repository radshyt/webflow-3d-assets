# Dither hero — Webflow setup

Three code blocks plus a model file. Total weight on the wire is about
3.4 MB, nearly all of it the model, loaded asynchronously after paint.

## Install

**1. Host the model.** Webflow's asset panel won't accept `.glb`. Put
`dyno.glb` on GitHub Pages, jsDelivr, Cloudinary or any host that sends
permissive CORS headers, then copy the URL.

**2. HTML** — drop an Embed element on the page, paste
`webflow-1-embed.html`, and set `data-model` to your URL.

**3. CSS** — Page Settings → Custom Code → Inside `<head>`, wrapped in
`<style>` tags. Paste `webflow-2-styles.css`.

**4. JS** — Page Settings → Custom Code → Before `</body>`, wrapped in
`<script type="module">` tags. Paste `webflow-3-script.js`. The
`type="module"` is not optional.

Note: the file's opening comment writes the closing tag as `<\/script>` on
purpose. A literal closing tag inside an inline script ends it early.

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
| Rotation speed | `spinSpeed` (rad/sec) | `0.22` |
| Slow vertical drift | `wobbleAmount`, `wobbleSpeed` | `0.10`, `0.30` |
| Cursor response | `mouseStrength` | `0.38` |
| Cursor weight/lag | `mouseEase` (lower = heavier) | `0.055` |
| Slide toward cursor | `mouseParallax` | `0.05` |
| Object size in frame | `fitHeight` (frame is ~2.9 tall) | `1.90` |
| Resting angle | `baseRotationX`, `baseRotationY` | `0` |
| Beam brightness | `rayIntensity` | `1.95` |
| How far beams reach | `rayReach` | `0.86` |
| Beam length / falloff | `rayDensity`, `rayDecay` | `1.00`, `0.990` |
| Beam thickness | `lightCoreSize` (smaller = tighter) | `0.060` |
| Central glow spread | `lightHaloSize`, `lightHaloGain` | `0.55`, `0.60` |
| Light position | `lightCenterX/Y` (0–1) | `0.5` |
| Chrome sharpness | `roughness` (lower = mirror) | `0.13` |
| Reflection strength | `envIntensity` | `1.05` |
| Grey steps in the dither | `ditherLevels` (2 = 1-bit) | `6` |
| Dither dot size | `ditherPixelSize` (CSS px) | `2` |
| Overall grade | `exposure`, `contrast`, `lift`, `vignette` | — |

Also available: `ditherHero.stop()`, `.start()`, `.destroy()`.

### Notes on the look

The beams are real volumetric light — a black silhouette of the object
occluding a bright centre, radially blurred — so the shape of the model
determines the shape of the beams. A solid mass throws broad wedges; a
shape with gaps throws thin shafts. If you want finer beams from the
dino, reduce `lightCoreSize` rather than touching the blur settings.

By default the letters do not block the light, matching your reference.
Set `textOccludesLight: true` to have the words cast ray shadows too.

## Mobile

The effect runs on phones. Devices matching `(hover: none)` or
`(pointer: coarse)` automatically drop to a 1.25 pixel-ratio cap, 16 ray
samples and a 0.22 occlusion buffer.

Touch position drives the rotation in place of the cursor. Because a
phone has no hover state, the object sways gently on its own until the
first touch — `touchDrift` controls that, set it to `0` to disable.

`prefers-reduced-motion` stops the spin and the pointer response.

## Performance

Four passes per frame: occlusion at 30% resolution, a 24-tap radial blur
at that same size, the beauty pass, then the grade-and-dither composite.
Only the beauty pass runs at full resolution.

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
