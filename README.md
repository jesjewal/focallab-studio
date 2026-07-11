# FocalLab Studio — Depth of Field & Lighting Pre-Viz

**Live: [focallab-studio.netlify.app](https://focallab-studio.netlify.app)**

FocalLab Studio is a single-file pre-visualization studio for photographers and filmmakers, born as a rebuild of the classic DoF-simulator concept and evolved well past it: pick a sensor, lens, aperture and distances, place up to four people at real depths, drag lights around an overhead stage — and watch a live viewfinder render physically accurate blur, bokeh, and lighting while the full optics math updates beside it.

**One file, zero dependencies.** Open `index.html` in any modern browser, or serve the folder:

```
python3 -m http.server 8943 --directory .
```

## Features

### Photo + Video modes
- **Photo**: stills sensors (medium format 44×33 → 1/2.3" compact), ƒ-stops, landscape/portrait.
- **Video**: cine sensors (Super 16, Super 35, Alexa LF, VistaVision/RED VV, Alexa 65), T-stops, aspect ratios (16:9, 1.85:1, 2:1, 2.39:1 — treated as a sensor-height crop), REC/timecode HUD with action-safe guides and center crosshair.

### Optics (the core)
- Live viewfinder where subject scale comes from the real field of view at the focus distance; every point light is drawn at its computed blur-disc diameter (true bokeh balls); extended surfaces get matching gaussian blur; background, far plane (~6×) and stars/sun (∞) blur independently.
- Full stats: DoF + front/behind split, near/far limits, hyperfocal with "focus there" hint, FoV + frame size at subject, magnification + framing label, bokeh disc in mm and % of frame, full-frame equivalent for crops.
- Top-view diagram: FoV wedge, green DoF band (√-scaled axis), hyperfocal marker, ∞ handling.

### People — a real cast, not a cardboard cutout
- Build a roster of up to **4 people**, added one at a time: 10 types (woman, man, child, runner, soccer player, basketball player, performer, cyclist, seated, dog), 6 skin tones, per-type clothing palettes so same-type people stay distinct.
- Every person has their own **lateral position (in metres), height, and distance from camera** — zooming in crops edge people out of frame instead of sliding them around, exactly like recomposing a real shot. Off the focus plane, each person softens by their true blur disc.
- **Click-to-focus**: click anyone in the viewfinder to rack focus to them. Each roster row has an **AF lock** switch (one at a time) — focus follows that person as you move them, like a focus puller tracking an actor.
- **Upload a person** (free): drop in a photo, the background is removed client-side (or bring a pre-cut PNG), and they join the scene as a sprite with position, height, depth, and lighting.
- **AI people & backdrops** (bring your own Gemini key): describe a person or a background plate and generate it — people arrive on a chroma-green plate and are keyed out in the browser. The key lives in your localStorage only; nothing touches a server.

### Lighting designer
- Schemes from Natural/ambient to 3-point and Backlit/silhouette — or build a **custom rig of up to 6 lights**, each with its own position, power, and color temperature (3200K/5600K/7500K).
- **Overhead stage**: drag lights around the subject on distance rings; every person is lit by their real distance and angle to each light (inverse-square falloff), so a light near one actor barely touches another six metres back.
- Physically motivated: frontal lights shade the figure directionally, rear lights become edge rims, the ground shadow shifts opposite the key, and on-camera flash spills onto the background at `(s/b)²` of subject exposure — with a "Flash reach" stat card that makes the inverse-square lesson explicit.

### Locations
- 13 procedural scenes across day and night: sunny meadow, city street day, overcast park, city park dusk, forest golden hour, beach sunset, stadium night, basketball arena, theater stage, festival stage, neon street, and a soundstage built for the lighting designer.
- **Upload your own backdrop photo** — it becomes the background plane and blurs by the real computed disc for your settings.

### Learn — guided lessons for beginners
- A **Learn** button with three tracks: a quick tour of the interface, **Photography basics**, and **Video basics** — written in plain language for people who have never touched a camera.
- Lessons drive the live viewfinder: the aperture step actually snaps between f/1.8 and f/11 so you watch the background change; the rack-focus step stages two people and has you click between them.

### Save & share
- **Storyboard** (Video): save shots with thumbnails, name them, reorder/duplicate — build coverage shot by shot.
- **Saved looks** (Photo): keep editorial/portrait recipes. Both persist in the browser (localStorage; uploads are not stored).
- Shareable URLs: full state (mode, aspect, people, lighting rig) lives in the hash; Copy Settings Link button.
- **PDF export** — one click prints a sheet with the current frame as hero plus every saved shot, each captioned with lens, distances, DoF, and blur.

### Interface
- **One-screen cockpit** — three-panel layout, and every pane collapses to its title bar (remembered between visits) so you can strip the UI down to what you're using.
- **Units** — meters or feet & inches, auto-picked from the browser locale (US → ft/in); header toggle persists your override. Sensors and focal lengths stay in mm, as photographers expect.
- **Dark & light themes** — true black / pure white, following the flask-and-aperture brand mark; follows the OS automatically, header toggle overrides.
- **Installable PWA** — web manifest + service worker (network-first shell, offline-capable); responsive down to phones.

## Physics

Thin-lens model, camera at 1.5 m eye height. CoC `c = active-area diagonal ÷ 1500`. Hyperfocal `H = f²/(N·c) + f`; near `s(H−f)/(H+s−2f)`; far `s(H−f)/(H−s)` (∞ when s ≥ H); blur disc at x: `b = (f²/N)·|x−s|/(x·(s−f))`. T-stops are used as the f-number for depth purposes. Math block delimited by `==MATH-START==/==MATH-END==` markers — extract with `sed`, unit-test with node against DOFMaster references.

Not simulated: lens character (cat-eye, swirl, onion rings), focus breathing, motion blur/shutter angle, soft-box quality vs hard light.

## Ideas for later

- Auto scene-set: solve lens, framing, people placement, DoF, and lighting from a chosen shot type (staged solver, not a search)
- Shutter-angle motion blur on the sports subjects
- Named camera/lens picker; foreground blur plane; aperture-blade bokeh shapes
- Bokeh extraction from uploaded backdrops (find bright points, render them as discs)

Original build — not affiliated with dofsimulator.net. Built by Jesse Wallace, 2026.
