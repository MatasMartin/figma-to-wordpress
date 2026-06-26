# Demo assets

`demo.png` (shipped, shown in the README) is a **real proof image**: the values in its right-hand column are genuine `dom-measure.js` output measured against a demo component built to exact "from-Figma" values - not a mockup. It demonstrates the method (guess ✗ → read the node ✓ → measure to verify ✓) with no client data.

This file tracks the optional **upgrade**: a live-action before/after GIF of Claude Code actually doing it.

## Why the GIF needs you (not Claude)
A screen recording of the live session needs a recorder running on your machine, and a **throwaway, non-client** Figma file + scratch local WordPress site. Don't screen-record any client build.

## How to make the real demo (≈30-60 min)
1. **Throwaway Figma frame.** One marketing section (hero or a 3-card row) with a few deliberately specific values - e.g. radius 8px, 13px Medium, a brand hex. The "specific values" are the point: they're what eyeballing gets wrong.
2. **Scratch WordPress site** in LocalWP on any builder (Elementor is the broadest audience).
3. **Record the loop** (Kap / QuickTime / `ffmpeg`):
   - Left: the Figma frame in Dev Mode showing the real values.
   - Right: Claude Code building it, then running `scripts/dom-measure.js` and showing the measured values matching the Figma node.
   - The "wow" beat is the **measurement matching** - that's the differentiator, not the build itself.
4. **Export** a < 10 MB GIF or MP4 → save as `assets/demo.gif` (or `.mp4`).
5. **Wire it into the README** - replace the "demo GIF coming soon" blockquote with:
   ```markdown
   ![Figma → measured-and-matched WordPress page](assets/demo.gif)
   ```

## Optional: a static "method" image
If a GIF is too much up front, a single clean before/after still image (Figma values vs. the matching measured DOM values, side by side) reads well in a Reddit post and as the repo's social preview. Same rule: throwaway content only.
