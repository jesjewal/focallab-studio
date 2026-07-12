# FocalLab Studio — Working Guide

Single-file DoF + lighting pre-visualization studio for photo/video.
**Live:** https://focallab-studio.netlify.app · **Repo:** github.com/jesjewal/focallab-studio
Everything is in `index.html` (~2,400 lines). Zero dependencies. Do not add build steps, frameworks, or external requests (the only network call is the user's own Gemini key → Google, browser-direct).

## Hard rules (from Jesse — do not violate)

1. **ASK before every `git push` and every `netlify deploy`.** Netlify tokens are metered. Never auto-deploy.
2. **AI features must cost Jesse nothing.** Bring-your-own-key only (localStorage `focallab.gemini`); never introduce a server or shared key.
3. **Bump `sw.js` cache name** (`focallab-vNN`) whenever any cached asset (`ASSETS` list) changes. `index.html` itself is network-first, so pure index changes don't strictly require a bump.
4. **"Video", never "Cinema"** in user-visible copy (internal state key stays `'cine'`).
5. **Lesson copy = plain language, ~8th-grade reading level.** No sass, no jargon-as-personality. Audience is true beginners.
6. **No trailing summaries** in responses to Jesse; he reads diffs. Direct and practical.
7. Design must **fit, not gimmick**: brand touches were deliberately pruned (see Design). Don't reintroduce ruler bars / film strips / texture layers without being asked.

## Deploy / verify workflow

```bash
# local preview (or .claude/launch.json config "focal-lab" in the VS CODE workspace)
python3 -m http.server 8943 --directory .

# deploy (ONLY after Jesse approves)
git push origin main
netlify deploy --prod --dir .

# verify live
curl -s https://focallab-studio.netlify.app/sw.js | head -1     # cache version
curl -s https://focallab-studio.netlify.app/ | grep -c "<marker>" # new markup present
```

`netlify.toml`: publish ".", no-cache headers for index.html + sw.js. GitHub→Netlify auto-deploy is NOT connected (open loop).

## Architecture map (all in index.html)

| System | Anchor / key names | Notes |
|---|---|---|
| Optics math | `//==MATH-START==` … `//==MATH-END==` | Thin lens; CoC = diagonal/1500; `dofLimits`, `hyperfocal`, `blurDiscAt`, `fovAngles`. **Regression-test after touching:** `sed -n '/==MATH-START==/,/==MATH-END==/p' index.html > /tmp/m.js` then node-assert vs DOFMaster (FF 50/1.8@3m → near 2836mm, far 3184mm; disc 85/1.8 s=2.5m x=10m → 1.247mm). |
| State | `const state={mode,cam,o,ar,f,N,s,b,afP,people,scene,lights,grid,hud,ls,rig,ui,...}` | `s`=focus dist (m), `N`=aperture, `b`=background dist. Shared via URL hash (`queueHash`); `afP` and `ui` are session/local-only. |
| Render loop | `render()` → rAF → `clampState(); syncInputs(); compute(); drawScene(); drawTopView(); drawStats(); renderOverhead(); queueHash()` | In hidden preview tabs rAF may never fire — for scripted tests call `clampState(); syncInputs(); drawScene(compute());` synchronously. |
| People roster | `state.people` (≤4): `{t,skin,x,h,d,c,hair,[sprite,label,ico]}` | `x` lateral **metres** (zoom crops people out, never slides them), `d` per-person depth, blur from real disc. `normPeople()` validates/migrates; hash key `ppl=t_skin_x_h_d~...` (underscores — values contain '.'). |
| Click-to-focus / AF | `PHIT` (per-frame hitboxes in `drawPeople`), `state.afP` | Canvas click racks `state.s` and moves the lock; `.paf` switches exclusive; depth slider follows lock; manual `#dist` drag releases; `focusedPerson()` prefers the lock. |
| Lighting rig | `state.rig` (≤6 lights `{on,phi,d,i,t,role}`), `SCHEMES`, `applyFigLighting(...)` | Per-person inverse-square by real distance+angle. Brightest light drives ground shadow. |
| AI generation | `geminiImage(prompt,aspect)`, `chromaKey(img)`, `SPRITES{}` | Model `gemini-2.5-flash-image:generateContent`, `responseModalities:['IMAGE']` + `imageConfig:{aspectRatio}` w/ retry-without on 400. People generated on chroma green, keyed client-side. Sprites are session-only; `normPeople` degrades `t:'ai'` → `'woman'` when sprite missing. |
| Upload person | `showAvatar(src,name)`, `autoKey(img,strength)`, `trimAlpha(c)` | Border-sampled background key, strength slider; pre-cut PNG alpha detected → keyer off. |
| Simple/Pro | `setUI(u)`, `#uiSeg`, `body.ui-simple`, `.proOnly`, `.stat.pro` | Simple = default for new visitors (localStorage `focallab.ui`). Pro-only: aspect, backdrop upload/AI, upload/AI person buttons, overhead rig, top view, math panel, Display card, Hyperfocal + FoV stats. **Anything new/advanced should be tagged `.proOnly`** unless it's core. Lessons call `setUI('pro')`. |
| Collapsible panes | IIFE near `// ---------- collapsible panes` | Every `.card` with an `h2` collapses (`.min`), persisted in `focallab.panes`. New cards get this for free if they have an `<h2>`. |
| Guided learning | `TOURS={tour,photo,cine}` in the tour IIFE near EOF | Steps: `{sel,spot,title,body,before:()=>}`; `before` drives live state. **New features must be added to the relevant tour(s)** — Jesse checks. |
| Saves/share | `shotsKey()`, localStorage per mode; hash URLs; PDF via `#printSheet` + print CSS | Uploaded/AI imagery never persists. |
| PWA | `manifest.webmanifest`, `sw.js` (network-first shell, cache-first assets) | Icons: `fcl-icon.png`. |

## Design system (current, post-redesign 2026-07-11)

- **Themes:** dark = true black `#000` + neutral grays; light = pure white. All vars in `:root` / `html.light`. Accent amber `#f0a93c` (dark) / `#c07300` (light). Functional colors: `--good` (AF on), `--teal`, `--hot`.
- **App bar** (`#appBar`): flask mark `fcl-mark.png` (40px, `filter:invert(1)` in dark) + live-text wordmark (`.wordmark`, letterspaced caps, STUDIO sub-line) left; toolbar (`#topbtns`: Simple/Pro seg, Learn, units, theme, share) right; 1px bottom border. The full lockup image appears ONLY in og-card/README — not in the UI.
- **Kept motifs:** AF corner brackets on `.frame::after`; small bubble markers on pane titles (fill amber when collapsed). **Removed on purpose:** ruler bar, film-strip divider, graph-paper grid.
- **Known debt (next design pass, "C-pass"):** accent is still overused (mode pill, all sliders, all values) — reserve it for values/active states; type scale is small (10–13px labels); person rows are dense slider stacks. Longer-term direction Jesse liked: camera-first layout (viewfinder hero, bottom tab deck) — mock it up before building.

## Brand assets (regenerate with PIL — sources gitignored)

Sources on disk: `fcl-logo-black.png` / `fcl-logo-white.png` (5016², real alpha), `*.jpg` scans.
- `fcl-logo.png` — full lockup, trimmed, 520px tall (share/README use)
- `fcl-mark.png` — flask only: crop above first big transparent row-gap, trim, 160px tall
- `fcl-icon.png` — flask mark centered on white 512² (favicon/PWA)
- `og-card.png` — 1200×630: black bg, faint 30px grid, white lockup, tagline in spaced HelveticaNeue
Recipe pattern: load black PNG → crop alpha bbox → (split at row-gap for mark) → resize LANCZOS → save optimized.

## Content guidelines

- UI copy: short, concrete, camera-native ("Focus dist", "rack focus", "AF"). Tooltips explain *why*, not just *what*.
- Stats cards: number first, plain-language subtitle ("focus there → sharp from X to ∞").
- Lessons: acknowledge physical truths simply (bigger sensor = more light collected = cleaner in dim scenes). One idea per step; each step should *change something visible* via `before:()`.
- Simple mode is the beginner's face of the app — never let a new pro-grade control leak into it by default.

## Testing recipes that work here

- **Preview quirks:** hidden tabs throttle rAF (force draws synchronously, see above) and screenshots after scrolling can capture stale/black frames — verify via `read_page`/JS state instead, or resize the viewport tall.
- **Scripted interaction tests** (used for AF): dispatch `MouseEvent('click')` at PHIT-derived canvas coords; drive sliders with `dispatchEvent(new Event('input',{bubbles:true}))`.
- After edits: reload preview, check console errors, exercise the changed flow, screenshot both themes if visual.

## Open loops / backlog (priority order)

1. Jesse to test AI generation with his real Gemini key (only unverified path).
2. Connect GitHub→Netlify auto-deploy; buy focallab.studio domain.
3. Design C-pass: accent discipline, type scale, person-row density.
4. **Auto scene-set** (designed, not built): staged solver — intent dropdown (shot type) → solve lens+distance for target fill% → place people on thirds/staggered depth inside FoV → solve aperture/focus for DoF intent (cover spread vs isolate hero) → place key/fill/rim by convention. Deterministic pipeline, no search; respect locks; one-undo proposal. Add a "vary" shuffle.
5. Portfolio link from jesseleewallace.com; footer contact decision (portfolio link vs email).
6. Ideas parking lot: named camera/lens picker, foreground blur plane, aperture-blade bokeh, motion-blur note for sports subjects, bokeh extraction from uploaded backdrops.

## Session hygiene

Log every work block to `daily/YYYY-MM-DD.md` (VS CODE workspace) and `~/Documents/GitHub/wallacebrain/Daily Notes/` + `Projects/FocalLab.md`, and update auto-memory `project_focal_lab.md`. Commit locally as you go; **push/deploy only with approval**.
