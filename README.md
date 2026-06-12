# FocalLab Studio — Depth of Field & Lighting Pre-Viz

FocalLab Studio (focallab.studio) is a modern, near-single-file pre-visualization studio for photographers and filmmakers, born as a rebuild of the classic DoF-simulator concept and evolved well past it: pick a sensor, lens, aperture and distances, place your lights on an overhead stage, and watch a live viewfinder render physically accurate blur, bokeh, and lighting while the full optics math updates beside it.

**One file, zero dependencies.** Open `index.html` in any modern browser, or serve the folder:

```
python3 -m http.server 8943 --directory .
```

## Features

### Photo + Cinema modes
- **Photo**: stills sensors (medium format 44×33 → 1/2.3" compact), ƒ-stops, landscape/portrait.
- **Cinema**: cine sensors (Super 16, Super 35, Alexa LF, VistaVision/RED VV, Alexa 65), T-stops, aspect ratios (16:9, 1.85:1, 2:1, 2.39:1 — treated as a sensor-height crop), REC/timecode HUD with action-safe guides and center crosshair.

### Optics (the core)
- Live viewfinder where subject scale comes from the real field of view at the focus distance; every point light is drawn at its computed blur-disc diameter (true bokeh balls); extended surfaces get matching gaussian blur; background, far plane (~6×) and stars/sun (∞) blur independently.
- Full stats: DoF + front/behind split, near/far limits, hyperfocal with "focus there" hint, FoV + frame size at subject, magnification + framing label, bokeh disc in mm and % of frame, full-frame equivalent for crops.
- Top-view diagram: FoV wedge, green DoF band (√-scaled axis), hyperfocal marker, ∞ handling.

### Lighting designer
- Schemes: Natural/ambient, On-camera flash, Key only, Key + fill, 3-point, Backlit/silhouette — or drag into a Custom setup.
- **Overhead stage**: drag key/fill/rim pucks around the subject (distance rings in meters); per-light power slider and color temperature (3200K/5600K/7500K).
- Physically motivated: inverse-square falloff, frontal lights shade the figure directionally, rear lights become edge rims, the ground shadow shifts opposite the key, and on-camera flash spills onto the background at `(s/b)²` of subject exposure — with a "Flash reach" stat card that makes the inverse-square lesson explicit.

### Subjects & locations
- 5 subjects: woman, man, family of three, runner, soccer player (composable capsule-stroke silhouettes; AF box tracks each subject's face, incl. the child's).
- 6 procedural locations: city park dusk, forest golden hour (sun-dapple bokeh), beach sunset (sun glitter, palms, soft sun ball), stadium night match (LED hoardings, crowd speckle, floodlights, goal), neon street (marquee-bulb signs with wet-asphalt reflections, doorway glow), and a soundstage (cyc wall, softboxes on stands, standby LEDs — made for the lighting designer).
- **Subject placement**: slide the subject left/right across the frame (rule-of-thirds composition) and flip their facing — AF box and shadow track along.
- **Upload your own backdrop photo** — it becomes the background plane and blurs by the real computed disc for your settings.

### Save & share
- **Storyboard** (Cinema): save shots with thumbnails, name them, reorder/duplicate — build coverage shot by shot.
- **Saved looks** (Photo): keep editorial/portrait recipes. Both persist in the browser (localStorage; uploaded backdrops are not stored).
- Shareable URLs: full state (incl. mode, aspect, lighting scheme) lives in the hash; Copy Settings Link button.

### Interface
- **One-screen cockpit** — three-panel layout (camera/lens/scene · viewfinder/diagram/stats · presets/lighting/saves) fits a 1440×900 display without scrolling; viewfinder size S/M/L control (L deliberately pushes panels below the fold).
- **Units** — meters or feet & inches, auto-picked from the browser locale (US → ft/in), header toggle persists your override. Sensors and focal lengths stay in mm, as photographers expect.
- **Dark & light themes** — follows the OS `prefers-color-scheme` automatically; the header toggle overrides and remembers your choice. The viewfinder stays dark in both (it's a camera back).
- **PDF export** — one click prints a sheet (browser "Save as PDF") with the current frame as hero plus every saved shot, each captioned with lens, distances, DoF, and blur.
- **Installable PWA** — web manifest + service worker (network-first shell, offline-capable). On a phone: "Add to Home Screen" gives a standalone, app-like experience. Fully responsive down to ~375px.

## Physics

Thin-lens model, camera at 1.5 m eye height. CoC `c = active-area diagonal ÷ 1500`. Hyperfocal `H = f²/(N·c) + f`; near `s(H−f)/(H+s−2f)`; far `s(H−f)/(H−s)` (∞ when s ≥ H); blur disc at x: `b = (f²/N)·|x−s|/(x·(s−f))`. T-stops are used as the f-number for depth purposes. Math block delimited by `==MATH-START==/==MATH-END==` markers — extract with `sed`, unit-test with node against DOFMaster references.

Not simulated: lens character (cat-eye, swirl, onion rings), focus breathing, motion blur/shutter angle, soft-box quality vs hard light.

## Ideas for later

- Deploy (Netlify/Vercel — single static file)
- Shutter-angle motion blur on the sports subjects
- Named camera/lens picker; foreground blur plane; aperture-blade bokeh shapes
- More subjects (couple, cyclist, dog) and locations (snowy holiday lights, café interior)
- Bokeh extraction from uploaded backdrops (find bright points, render them as discs)

Original build — not affiliated with dofsimulator.net. Built by Jesse Wallace, June 2026.
