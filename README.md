# Voidline Sector R-77

An atmospheric landing page built around a dark sci-fi painting. Designed to feel like a live surveillance feed monitoring an anomalous event.

## Files

```
index.html                          — entire page (self-contained)
Voidline_Sector_R-77-viewport01.png — background image (required, same folder)
```

## Running locally

Requires a local server due to browser CORS restrictions on `file://` origins.

```bash
cd /path/to/voidline.gg
python3 -m http.server 8080
# open http://localhost:8080
```

## What it does

- Background image always fully visible (`object-fit: contain`), black bars fill leftover space
- Canvas layer above the image renders animated rain and glitch effects
- Glitch engine fires every 6–12 seconds, one effect at a time:
  - **Slice** (most frequent) — horizontal bands of the image displace sideways
  - **Scanline blocks** — small dark rectangles with cyan scanline texture drop over the image
  - **RGB convergence** (rarest) — chromatic split that slowly re-aligns, also plays on page load
- UI elements (top bar, title, bottom data strip) fade in sequentially on load
- Responsive breakpoint at 705px for mobile

## Tuning

**Glitch frequency** — search `gTimer=rand(6,12)` (appears twice). Change the range to taste.

**Glitch probabilities** — find `triggerGlitch()` in the JS. Roll thresholds are cumulative:
```js
if(roll < 0.38)      // slice
else if(roll < 0.75) // scanline
else                 // RGB
```

**Glitch durations** — set inside each branch of `triggerGlitch()`. Scanline is fixed at `1.0s`.

**Rain** — find `mkDrop()`. `op` controls opacity, `len` controls streak length, `w` controls thickness.
