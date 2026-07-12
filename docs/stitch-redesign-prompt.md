# Google Stitch — FocalLab Studio redesign brief

**Tool:** https://stitch.withgoogle.com · pick **Web**, desktop frame (~1440px).
**Reference image to upload:** `fcl-logo.png` (the full flask + aperture + "FOCAL LAB STUDIO" lockup). Upload it so Stitch matches the mark's line weight, spacing, and monochrome feel. Optionally also upload a screenshot of the current app so it knows what it's rebuilding.

Paste the prompt below. Generate, then iterate with the follow-up nudges at the bottom.

---

## PROMPT

Design a desktop web app UI for **FocalLab Studio**, a depth-of-field and lighting pre-visualization studio for photographers and filmmakers. The whole interface must feel like a direct extension of the attached logo: a fine-lined flask-and-camera-aperture mark, pure black-and-white, scientific and precise, with generous negative space and wide letter-spacing. Think laboratory instrument meets cinema camera — calm, exact, and premium, not busy.

**Brand & visual system**
- Monochrome foundation: true black background (`#000`) in dark mode, pure white in light mode. Neutral grays for panels, hairline 1px borders.
- Exactly ONE accent color — a warm amber — used ONLY for live data values (numbers, the active reading on a slider), never for buttons, backgrounds, or decoration. Controls are monochrome ink.
- Typography: a clean geometric sans. Section labels in small UPPERCASE with wide letter-spacing (echo the "F O C A L  L A B" spacing in the logo). Numeric readouts in a monospace face.
- Thin strokes, rounded-rectangle cards, lots of breathing room. The mark's aesthetic — precise line art on black — should set the tone.

**Masthead**
- A confident top bar. The flask mark and the "FOCAL LAB / STUDIO" wordmark sit together, sized to command the top-left with real presence (mark ~56px tall). Right side holds a small pill toggle "Simple / Pro", plus quiet icon buttons: Learn, units (ft/m), light/dark, and "Copy settings link". A hairline divider under the bar.

**Layout — this is the key problem to solve**
The app has a LOT of controls but must feel airy, not packed. Make **the live viewfinder the clear hero** — a large 16:9 camera-back image, centered and dominant, with thin AF-style corner brackets framing it. Everything else is supporting instrumentation that should **flow and balance to fill the space around the hero** — no rigid tall side columns with empty gaps, and no dead voids. Prefer a responsive card/tile system (masonry-like) where control panels reflow to fill available width and fold under gracefully as the window narrows, keeping the composition balanced at every size. Beneath the viewfinder, a clean even row of stat tiles (see below).

**Panels that must be present** (as clean cards with UPPERCASE labels):
- CAMERA: a "Photo / Video" segmented toggle, a sensor dropdown, orientation toggle.
- LENS: two sliders — Focal length (mm) and Aperture (ƒ-stop) — each with a monospace amber value.
- SCENE: a Location dropdown; "Upload backdrop" / "Generate" buttons; a PEOPLE list where each person is a compact row (color swatch, type, an "AF" focus-lock chip); and Focus/Background distance sliders.
- AUTO-SET THE SHOT: a shot-type dropdown (Portrait, Two-shot, Group, Wide…) and a primary "Auto-set" button plus "Vary". This is the hero control for beginners — give it a subtle solid (ink-filled) primary button.
- LIGHTING: a scheme dropdown and a circular top-down "stage" where light pucks can be dragged around the subject, with faint concentric distance rings.
- Stat tiles under the viewfinder: Depth of field, In focus, Background blur, Hyperfocal, Field of view, Framing — each a small tile with an UPPERCASE label, a large mono number (amber), and a gray sub-line.
- A wide "TOP VIEW" diagram card (a side-profile focus-plane / depth-of-field chart).
- Save / PDF / Presets controls.

**Modes**
- "Simple" shows only the essentials (camera, lens, scene, auto-set, lighting scheme, the core stat tiles) with lots of air.
- "Pro" reveals everything (the light-rig stage, top-view diagram, extra stats, display toggles).

**Two themes**: design both dark (white-on-black, the primary look) and light (black-on-white). Both must feel like the same instrument.

Overall vibe: a precision optical instrument's control surface — the calm, confident, lab-grade feeling of the logo, extended across the entire screen.

---

## Follow-up nudges (after the first generation)
- "Make the viewfinder larger and more dominant; shrink the surrounding cards."
- "More negative space between cards; thinner borders; reduce visual weight."
- "Reserve the amber accent strictly for numeric values — make every button and toggle monochrome."
- "Show the light layout as a top-down circular stage with concentric distance rings."
- "Give me the light theme version of this exact layout."
- "Tighten the vertical rhythm so nothing leaves large empty gaps below it."

## When you have a direction you like
Screenshot it (or export), hand it back to me, and I'll translate the winning layout into the real `index.html` — keeping all the working physics, auto-set solver, and interactions intact.
