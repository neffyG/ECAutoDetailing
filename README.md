# EC Auto Detailing — Premium Site

A cinematic, premium single-page site for EC Auto Detailing featuring a scroll-driven
car animation built from 121 video frames rendered to an HTML canvas.

## What's in this bundle

```
ec-auto-detailing/
├── index.html          # the site (single file, all CSS + JS inline)
├── frames/             # 121 desktop frames, 1400px wide (~8.5 MB total)
└── frames_mobile/      # 121 mobile frames, 720px wide (~3.3 MB total)
```

The site loads the desktop frames on screens wider than 820px and the mobile frames
on narrower screens. This keeps mobile data use under 4 MB while keeping desktop crisp.

## How to run it locally

Because the site fetches frame images over HTTP, you can't just double-click
`index.html` — you need a simple local web server. Pick whichever is easiest:

**Python (already on Mac/Linux, comes with ASU CS setup):**
```bash
cd ec-auto-detailing
python3 -m http.server 8080
```
Then open http://localhost:8080 in your browser.

**Node:**
```bash
npx serve ec-auto-detailing
```

**VS Code:**
Install the "Live Server" extension, right-click `index.html`, "Open with Live Server".

## How to deploy

This is a fully static site — no build step, no dependencies.

Drop the whole folder onto any static host:
- **GitHub Pages**: push the folder to a repo, enable Pages on main branch
- **Netlify / Vercel**: drag-and-drop the folder into their dashboard
- **Cloudflare Pages**: same

The only outbound request is to Google Fonts for the typefaces (Fraunces,
Instrument Sans, JetBrains Mono). Everything else is local.

## Key technical notes

### Scroll-driven animation
- 121 frames drawn to a `<canvas>` inside a `position: sticky` container
- 450vh scroll track controls frame progress (feels generous, not cramped)
- DPR-aware canvas sizing, capped at 2x for performance
- Lerp-based tween between scroll target and current frame for silky scrub
- Preloader shows until the first 30 frames are decoded — animation never stutters at start
- `fetchpriority: high` on first 20 frames
- Separate mobile frame directory served via `matchMedia`

### Design system
- Obsidian black + deep crimson (drawn from the car itself) + warm gold accent
- Fraunces (display serif) / Instrument Sans (body) / JetBrains Mono (labels)
- SVG noise grain overlay, subtle radial auras, hairline rules
- IntersectionObserver scroll reveals with staggered delays
- `prefers-reduced-motion` respected — all transitions collapse to instant
- Fully responsive with clamp()-based typography

## Regenerating the frames

If you ever update the source video, regenerate frames with:
```bash
# Desktop
ffmpeg -i car.mp4 -vf "scale=1400:-2,fps=24" -q:v 4 frames/frame_%03d.jpg

# Mobile
ffmpeg -i car.mp4 -vf "scale=720:-2,fps=24" -q:v 5 frames_mobile/frame_%03d.jpg
```

Then update `TOTAL_FRAMES` in the `<script>` block inside `index.html` to match
the new frame count (currently 121).
