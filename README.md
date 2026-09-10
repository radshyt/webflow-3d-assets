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
`webflow-3-script.js` as-is; the module script tags are already in the file.

The file is kept under Webflow's 50,000-character footer limit by stripping
the explanatory comments from the code body. The CONFIG block at the top keeps
all of its comments, since that is the part you edit; the reasoning behind the
rest lives in this README. The body's blank lines are removed and its indentation halved as well — neither
matters to JavaScript or GLSL. Current size is about 48,500 characters.

Publish. Custom code doesn't run in the Designer canvas — use Preview or
the published site.

## Editing the phrase

Change `data-text` on the embed. `|` breaks lines:

```html
data-text="Rad Shyt|Labs"
```

At runtime: `ditherHero.setText('New|Phrase')`.

## Typography

`textFamily` accepts a CSS variable reference — `'var(--font--primary-family)'`,
`'var(--font--secondary-family)'`, or any other custom property on the stage or
an ancestor. `'auto'` is shorthand for the primary one, and a literal family
stack works too. The variable is resolved against the container, so anything
Webflow defines is available.

The old behaviour read `--font--primary-family`
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
| Object size in frame | `fitWidth` (widest silhouette ÷ frustum width) | `0.48` |
| Resting angle | `baseRotationX`, `baseRotationY` | `0` |
| Figure beam brightness | `rayIntensity` | `1.10` |
| Where a beam saturates | `rayGain` (higher = fatter) | `6.50` |
| Blackness between beams | `rayContrast` (>1 crushes haze) | `1.45` |
| Beam sample jitter | `rayJitter` (leave at 1) | `1.00` |
| Beam tidy-up blur | `rayBlur` (low-res texels, 0 = off) | `1.00` |
| Letter beam brightness | `textRayIntensity` | `0.75` |
| Beams off the chrome itself | `objectRayIntensity` | `0.85` |
| How hot a pixel must be to emit | `objectRayThreshold` | `0.12` |
| Where a highlight beam saturates | `objectRayGain` | `22.00` |
| Stop beams washing the letters | `beamProtect` | `0.55` |
| Remove the central wash | `floodCut` (1 = none survives) | `0.90` |
| Width of the wash estimate | `floodRadius` (low-res texels) | `9.00` |
| How far the cut reaches | `floodExtent` (width units) | `0.30` |
| Slide the figure sideways | `modelOffsetX` (fraction of width) | `0.00` |
| Portrait switch point | `portraitBreakpoint` (width/height) | `1.00` |
| Figure size in portrait | `portraitFitWidth` | `0.86` |
| Side clearance | `marginX` (fraction of width) | `0.06` |
| Top/bottom clearance | `marginY` | `0.07` |
| Letters emit from outline | `textLightEdge` (keep at 1) | `1.00` |
| Outline thickness | `textLightEdgeWidth` (beam-buffer px) | `3.50` |
| Figure height in portrait | `portraitModelOffsetY` | `0.15` |
| Headline width in portrait | `portraitTextFitWidth` | `0.86` |
| Headline drop in portrait | `portraitTextOffsetY` (corner layout off) | `-0.32` |
| Stack one word per line | `portraitStack` | `true` |
| Portrait figure nudge | `portraitModelOffsetX` / `Y` | `0.00` |
| Portrait reflections | `portraitEnvIntensity` | `1.10` |
| Portrait beam scale | `portraitBeamScale` | `0.30` |
| Glass when clear of the words | `portraitGlass` | `0.55` |
| Wake-up distance | `activateMargin` | `'75%'` |
| Bottom edge roll-off | `bottomFade` (fraction of height) | `0.18` |
| Letter beam saturation | `textRayGain` | `7.00` |
| Damp letter beams at centre | `textRayInner` | `0.26` |
| Figure blocks its own beams | `rayOcclusion` | `1.00` |
| Beams swing off the cursor | `rayCursorShift` (0 = pinned) | `0.07` |
| Hard cap on beam brightness | `rayCeiling` (never raise above 1) | `1.00` |
| Glow through the letters | `textLightIntensity` | `1.00` |
| Letter emission falloff | `textLightFalloff` (2.0 = flat) | `2.00` |
| How far beams reach | `rayReach` | `0.86` |
| Beam length / falloff | `rayDensity`, `rayDecay` | `1.00`, `0.990` |
| Size of the backlight | `lightCoreSize` (see note) | `0.090` |
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
| Dither dot size | `ditherPixelSize` (CSS px) | `1` |
| Text inner padding | `textPadding` (fraction of block) | `0.06` |
| Fine vertical trim | `textNudgeY` (fraction of block height) | `0.00` |
| Portrait beam reach | `portraitRayReach` | `1.90` |
| Sticky scroll on/off | `stickyEnabled` | `true` |
| Transition start / length | `stickyStart` / `stickyRange` (vh) | `0.10` / `0.90` |
| Parked size | `stickyWidth` (fraction of vw) | `0.10` |
| Parked anchor | `stickyAnchorX` / `Y` (0–1) | `1.00` / `0.00` |
| Edge clearance when parked | `stickyMarginX` / `Y` | `0.05` |
| Canvas stacking | `stickyZIndex` | `1` |
| Gradient colour | `tintColor` (hex) | `'#ffffff'` |
| Colour amount | `tintStrength` (0 = greyscale) | `0.00` |
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

All light geometry — `lightCoreSize`, `lightHaloSize`, `rayReach`,
`textRayInner` — is measured in **width-relative** units, matching `fitWidth`
and `textFitWidth`. Everything therefore scales together: narrowing the window
shrinks the halo along with the figure and the type. These radii were previously
height-relative, which is why a downsized window left the halo at its old size.

**`lightCoreSize` must be larger than the figure's silhouette.** This is the
single most common way to lose the backlight: if the core is smaller than the
figure, the figure covers it completely, no light escapes around the edges,
and the only light left in the frame comes from the letters. At `0.140` the
backlight contributed nothing. `0.240` clears the silhouette. You can check
this at any time by setting `textRayIntensity: 0` — whatever remains is the
backlight, and if the frame goes black the core is too small.

`rayCursorShift` swings the beam **direction** with the cursor. The march
centre follows the pointer while the falloff and the lamp stay pinned to
`lightCenterX/Y` on their own uniform, so the beams sweep without the hot core
wandering. Past about `0.15` it starts to read as the light itself moving.

A compact core is what produces many thin spokes rather than a few broad
wedges — the reference look comes from light slipping around the silhouette in
slivers. `0.090` with a high `rayGain` gives that; a large core smears
everything into soft blobs.

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

### Glass, and why portrait uses a different value

`portraitGlass` replaces `glass` whenever the portrait layout is active. With the
figure clear of the words there is nothing behind it to refract, and at `glass:
1.00` a fully transmissive object over a black field has almost no silhouette to
read. Dropping toward chrome brings the form back. It is a separate value rather
than a compromise, so the landscape look is untouched.

## Glass

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

## A note on the canvas textures

The headline canvases are disposed and reallocated whenever their pixel
dimensions change. A `CanvasTexture` whose source changes size cannot simply be
flagged dirty: the GPU texture was allocated at the old dimensions, so a new,
smaller canvas gets uploaded into part of it and the previous headline survives
in the remainder. That is what produced a ghost copy of the words at the wrong
scale after resizing the browser — two headlines at different sizes on top of
each other. It only ever appeared after a resize, never on a fresh load at any
size, which is what identified it.

## Why the letter beams stay linear

A solid glyph is an **area** source. Radially blurred, it smears into a wedge,
and near the convergence point those wedges pile into a blob — dimming them only
made a dimmer blob. The letters therefore emit from their **outline** rather than
their fill: a thin source blurs into a thin linear shaft, so the rays stay
separate however the figure turns. `textLightEdge` at `1.0` is outline only;
drop toward `0` and the blob returns.

`textLightEdgeWidth` is measured in **beam-buffer pixels**, not glyph texels.
The glyph canvas runs at roughly `2 / (dpr x occlusionScale)` texels per beam
pixel, so an outline specified in texels was 0.58 of a beam pixel wide and
aliased away completely — the letters emitted nothing at all. In beam-buffer
pixels it converts correctly at any resolution, including phones, where the
pixel ratio and buffer scale both differ.

## Fitting the figure

The figure is scaled so its **widest silhouette at any yaw** fits the frustum
width, using the circumradius in the XZ plane. Spin is about Y, so that value is
exact at every angle — fitting to a single axis let the silhouette bleed off the
sides at some rotations, and a bounding sphere is safe but wastes room by
including the vertical diagonal.

The vertical extent adds an allowance for the X wobble and pointer tilt, which is
what was letting the head clip off the top edge. `marginX` and `marginY` are the
clearances kept on each side, and whichever constraint is tightest wins — so
there is always negative space around the figure at any viewport shape.

## The figure is the centre of its own light

`modelOffsetX` moves the **rig**, not the model inside it. Offsetting the model
within the rig left the pivot at the old screen centre, so the figure orbited a
distant point and swung in and out of the backlight. Moving the rig means it
spins about its own axis wherever it sits.

Every light effect derives its centre from `lightCenterX/Y` **plus** the figure's
offset — the backlight, the beam convergence, the letter falloff, the flood cut
and the vignette all travel with it. Nothing stays pinned to the screen.

## Portrait layout

Below `portraitBreakpoint` (width ÷ height, default `1.00`), `portraitStack` puts
every **word** on its own line, centred, ignoring the `|` breaks — so
`WE MAKE|RAD SHYT` becomes four stacked lines. `portraitTextFitWidth` sets how
much of the width the widest word fills and `portraitTextOffsetY` moves the block
vertically; both figure offsets default to `0.00`, so the figure sits centred.

**Portrait needs its own light values.** `portraitGlass` turns the figure part
chrome, and `envIntensity` tuned for full glass blows a chrome figure's
highlights out completely — hence `portraitEnvIntensity`. A chrome figure also
throws far more highlight light than a dark glass one, and full-frame stacked
type emits far more than a single line, so `portraitBeamScale` multiplies all
three beam intensities. Without those two the vertical frame washes to white:
measured 4.8% of pixels at white before, 2.2% after, with the median at pure
black.

Glass is re-applied on every resize. It previously ran only when the model
loaded, so `portraitGlass` took effect only if the page opened in portrait and
never when the window was resized into it.
the figure grows to `portraitFitWidth` and lifts by `portraitModelOffsetY`, and
the headline drops to `portraitTextOffsetY` so it always sits beneath the figure
rather than behind it. The five `portrait*` values are independent of their
landscape counterparts, so tuning one will not disturb the other.

## Bottom edge

Beams reaching the canvas floor used to stop dead against whatever section
follows. `bottomFade` rolls them off to black over that fraction of the canvas
height, at the bottom edge only. Measured: the bottom 8% of the frame goes from
0.0064 to 0.0010 mean while the frame overall is unchanged.

## Sizing and resizing

The headline block is measured once at a reference size to get its natural
aspect ratio. The canvas is then sized to the **text block**, and the plane it
is mapped onto is given exactly the same aspect. Width is `textFitWidth` of the
frustum width; height follows from the measurement.

This is the part that matters: previously the canvas took the *stage's* aspect
while the plane took the *camera's*, and any disagreement between those two
stretched the glyphs horizontally — a wrapper of a different width, a stale
layout read mid-resize, a container with padding. Both now derive from a single
measurement, so there is no second aspect ratio available to drift out of sync.
The type cannot be compressed by a resize regardless of what the surrounding
layout does. Correcting by ratio rather than shrinking once is what
stops the outer glyphs clipping, since letter-spacing means width does not
scale perfectly linearly with font size.

Only the container's inline width is ever read. Viewport height is not
consulted anywhere in the type path, so the phrase scales purely with width
and its proportions never change. Redraws are coalesced into one animation
frame, and it refits once webfonts settle.

If you move the stage into a different wrapper, keep the `.dyno_3d` class
and its `container-type` on the parent, or the type falls back to the
stage's own width.

The figure is scaled from the frustum **width**, so its on-screen size is a
function of viewport width and nothing else. Sizing it off height meant a
phone's toolbar sliding away made the viewport taller and the figure jumped
bigger mid-scroll. With `fitWidth` the pixel height works out to
`fitWidth x viewport width` exactly, whatever the height does.

On touch devices both the stage **and the `.dyno_3d` wrapper** are pinned to a
pixel height at load, revisited only when the width actually changes. Pinning
the stage alone still let a parent sized in `svh`/`dvh` grow when the toolbar
slid away, and that growth pushed the following section down mid-scroll. Do not
set a height on `.dyno_3d` in Webflow — let it come from the stage. A mobile browser collapsing or
expanding its toolbar changes the viewport height mid-scroll, and anything
sized off that height jumps; pinning means the toolbar can come and go
without the layout moving at all. Set `lockHeightOnTouch: false` to opt out.

## Sticky scroll

With `stickyEnabled`, the canvas is fixed to the viewport and never takes pointer
events, so the figure survives past its own section. Scrolling drives a single
progress value: the figure shrinks to `stickyWidth` of the viewport width and
travels to the bottom-right corner, `stickyMargin*` clear of the edges, over
`stickyRange` viewport heights starting at `stickyStart`.

The headline and all three beam sources fade out as it goes. The beams fade on a
**squared** curve so they are gone well before the figure parks — fading them
linearly left a large soft disc of light hanging in the middle of the transition,
which read as a smudge rather than as light.

Once parked, the resting spin is switched off and the figure turns only from
scroll. `scrollVel` is signed, so reversing scroll direction reverses the spin
with no extra handling. Pointer tilt, wobble and parallax all scale out with the
same progress value, so nothing keeps moving once it is in the corner.

The canvas is **transparent**. It clears to nothing and the composite writes its
own alpha from its own luminance, premultiplied — black areas end up fully
transparent, bright ones fully opaque. Over a black section the result is
indistinguishable from an opaque canvas (measured 0.0805 against an opaque
baseline of 0.0799), and over anything else only the figure and its light are
drawn. Nothing black is painted over the page.

Two details make that work. The composite uses `NoBlending` and covers the whole
screen, so it writes rgb and alpha straight to the framebuffer. And the clear
alpha stays at `1`: that same clear colour is used for the offscreen targets, and
setting it to `0` left the beauty pass compositing against nothing, which came
back blown out with the halftone crushed away.

Where the figure rests when parked is `stickyAnchorX`/`Y`, from `0` to `1` across
the space left inside the margins — `1, 0` is bottom-right, `0.5, 0.5` centres it.
`stickyMargin*` is measured from the viewport edge to the figure's own edge, so
the clearance is what you actually see.

`stickyZIndex` defaults to `1`. If your sections sit beneath the figure, raise
their own z-index rather than lowering this, or the canvas will end up behind an
opaque background.

## Text metrics

The headline block is measured from its **ink** — `actualBoundingBoxAscent` and
`Descent` — rather than the em box, so vertical centring is true for any face.
`textPadding` adds room around it because italic and script faces overhang their
advance width, which is what was clipping the last glyph. `textNudgeY` is a fine
manual trim on top.

## Colour

The gradient runs from `tintColor` at its bright end down to black.
`tintStrength` fades between untouched greyscale (`0`) and fully that colour
(`1`), so a mid value gives a desaturated wash rather than a different hue.

It is applied **after** the dither, as a multiply on the quantised grey. That
keeps black at exactly black and takes only the bright end to the colour, so the
halftone structure is preserved and simply carries colour instead of grey.
Verified: `#ff8a1e` at full strength renders its brightest pixel at exactly
`(1.000, 0.541, 0.118)`, and the darkest regions stay at pure zero.

The hex is parsed by hand rather than through `THREE.Color`, which would convert
sRGB to linear working space. These values multiply an already display-referred
result, so they have to stay exactly as authored.

## No flood, by construction

Dimming the beams never solved this, because the problem is not their peak but
the broad even lift that appears when the figure turns and lets more of the
source through. The blur pass now also takes a **wide** average of the beam
buffer and subtracts it. A wide average of a field of thin streaks is close to
its flood component, so subtracting it leaves the streak structure and removes
the wash — and because the term rises and falls with the flood itself, it is
self-levelling. Rotating the figure cannot wash the frame at any brightness.

The subtraction is weighted toward the centre and reaches only as far as
`floodExtent`. The wash originates where every beam converges, so cutting there
removes it while leaving the streaks further out completely alone — applied
evenly, as it was at first, it simply dimmed every beam in the frame.

Measured across three rotations: means within 6.8% of each other, medians at
pure black throughout.

## Three beam sources

The figure's own specular highlights emit. The beam pass samples the rendered
frame, takes everything above `objectRayThreshold` **within the figure's own
stencil**, and marches it like any other light. The stencil gate matters:
sampling the whole frame forced the threshold above the headline's value to stop
it flooding, and almost no pixels cleared that bar, so the channel was invisible
however hard it was driven. Its gain also sits in the composite ahead of the
power curve, like `rayGain` and `textRayGain` — applying it during the march
instead left values around 0.015, which the curve drove to nothing — so wherever the chrome catches a hot highlight, light streams off
it. Unlike the backlight and the letters, these beams are **not** masked by the
figure: the light starts on its surface, so it spills over the figure and
forward into the foreground rather than stopping at the silhouette. That is the
mechanism behind the reference, where light bleeds over and through the fingers.

This required reordering the frame. The beauty pass now runs first, because the
beam pass reads it.

Beams are screened over the image rather than added to it, and faded by
`beamProtect` wherever the image is already bright. Adding them meant the
letters took a full beam on top of their own value and flattened to white the
moment the figure turned and let more of the core through. Screening is bounded
by construction, so no combination of the three sources can push past white.

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

**`rayJitter` must stay at 1.0.** The march samples the occluder at discrete
steps, so at anything less than a full step the figure appears as a row of
ghost silhouettes marching outward from the centre — the same shape repeated
two dozen times. A full step smears them into a continuum. That trades the
stepping for noise, which is what `rayBlur` then cleans up: a 3x3 tent run on
the beam buffer at its own low resolution, far cheaper than widening the tap
count in the full-resolution composite. Turning `rayBlur` off brings the grain
back; lowering `rayJitter` brings the ghosts back. They work as a pair.

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

The pointer is normalised against the **viewport** when sticky, not against the
stage. The stage's rect scrolls away with the page, and normalising against it
once scrolled produced values far outside −1..1 — which drove the tilt past
vertical and stood the figure on its head until the cursor moved. The result is
clamped to ±1 in both modes regardless.

Pointer steering is mouse and pen only; touch pointer events are ignored.
Touch drags competed with page scrolling and read as jumpy, so on phones
scroll is the whole interaction.

`prefers-reduced-motion` stops the spin, the scroll response and the
pointer response.

## Mobile quality

Phones use their own values, applied whenever the device reports a coarse
pointer: `mobileDitherPixelSize` (2.00), `mobileRenderScale` (0.90),
`mobileMaxPixelRatio` (1.50), `mobileOcclusionScale` (0.26) and
`mobileRaySamples` (20). Raise `mobileDitherPixelSize` for a chunkier,
deliberate halftone; lower it for a finer grid. These replace the hardcoded
phone overrides that were previously not adjustable.

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
- Rendering starts `activateMargin` (default `75%` of the viewport) before the
  section reaches the screen, not when it arrives. The scroll spin accumulates
  from scroll deltas sampled in the render loop, so if the loop only woke on
  entry the spin visibly kicked in late — the figure would be turning at its
  resting speed and then jump. Scroll tracking also resets when the loop stops,
  so resuming never produces a single huge delta.
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
