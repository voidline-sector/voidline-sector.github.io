# Voidline Sector R-77

An atmospheric landing page built around dark sci-fi paintings. Designed to feel like a live surveillance feed monitoring an anomalous event.

## Files

```
index.html                                        — entire page (self-contained)
Voidline_Sector_R-77_viewport_01.jpg              — desktop landscape image
Voidline_Sector_R-77_viewport_02_portrait.jpg     — portrait image (mobile + desktop pool)
Voidline_Sector_R-77_viewport_03_portrait.jpg     — portrait image (mobile + desktop pool)
```

## Running locally

Requires a local server due to browser CORS restrictions on `file://` origins.

```bash
cd /path/to/voidline.gg
python3 -m http.server 8080
# open http://localhost:8080
```

## Image system

Each page load picks a random image from a pool:

- **Desktop:** all three viewports (01, 02, 03)
- **Mobile (≤705px):** portrait viewports only (02, 03)

Viewports 02 and 03 have the project name on an in-scene billboard, so the `.t-name` title element is hidden for those — only "TRANSMISSION ACTIVE" and the description remain visible. Viewport 01 shows the full title block.

Image pool and selection logic lives at the top of the JS, just after the clock setup. The `showTitle` and `portrait` flags on each entry control behaviour.

## Image format

Assets are JPEG rather than PNG. ~85% quality is recommended — the dark painterly content and rain grain make compression artefacts invisible at that level, and file sizes are roughly 4× smaller than equivalent PNGs.

## What it does

- Background image always fully visible (`object-fit: contain`), black bars fill leftover space
- Rain spans the full canvas including black bars — identical drops, no distinction between image and bar areas
- Canvas layer above the image renders animated rain and glitch effects
- Glitch engine fires every 6–12 seconds, one effect at a time:
  - **Slice** (most frequent) — horizontal bands of the image displace sideways
  - **Scanline blocks** — small dark rectangles with cyan scanline texture drop over the image
  - **RGB convergence** (rarest) — chromatic split that slowly re-aligns, also plays on page load
- UI elements (top bar, title, bottom data strip) fade in sequentially on load
- Responsive breakpoint at 705px for mobile

## Colour system

Two active tiers:

| Colour | Usage |
|--------|-------|
| Bright cyan `#4ee8d4` | All HUD text, labels, corner brackets, classification bars (filled) |
| Red `#e84e3a` | LIVE + dot, EXPANDING status + border, *unresolved* italic, classification bar dips |

`--dim` (`#2a7a70`) is defined in `:root` but currently unused — kept for future use if a third subdued tier is ever needed.

## Tuning

**Glitch frequency** — search `gTimer=rand(6,12)` (appears twice). Change the range to taste.

**Glitch probabilities** — find `triggerGlitch()` in the JS. Roll thresholds are cumulative:
```js
if(roll < 0.38)      // slice
else if(roll < 0.75) // scanline
else                 // RGB
```

**Glitch durations** — set inside each branch of `triggerGlitch()`. Scanline is fixed at `1.0s`.

**Glitch animation offsets** — randomized per page load via JS (not CSS). Find the block after the clock setup:
```js
['.t-name', '#coords', '#pressure'].forEach(sel => { ... })
```

**Rain brightness** — find `mkDrop()`. `op: rand(0.12, 0.42)` controls opacity range of all drops. Raise both numbers to brighten, lower to dim. The range width controls variance between faint and vivid streaks.

**Rain density** — `for(let i=0;i<220;i++)` in the rain setup. 220 drops span the full canvas. Density over the image will vary slightly depending on how much of the canvas the image occupies (portrait images on wide screens will appear sparser over the painting itself).

**Rain** — find `mkDrop()`. `op` controls opacity, `len` controls streak length, `w` controls thickness.
