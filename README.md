# figma-to-wordpress

**A Claude Code skill that stops the AI from guessing your Figma values.** It reads the real CSS from every node, builds it on your WordPress site, then *measures* the rendered page back against the design instead of eyeballing a screenshot.

Works with **Elementor, Gutenberg/blocks, and Divi**. Builder-agnostic at its core.

---

## The problem

Ask any AI to "build this Figma design in WordPress" and it looks at a screenshot and guesses: *"that button's about 14px, Bold, pill-shaped."* The values come out **approximately right and exactly wrong** — radius 44px when Figma says 8px, 14px when it's 13px, Bold when it's Medium. Each value is "close enough to look reasonable," so nothing trips an alarm. You ship a whole site with subtly wrong typography and corner radii, and the designer catches it in review.

## The method

Three rules, and the whole skill is built around them:

1. **Never guess — read the node.** Before stating any element's font-size / radius / padding / colour, pull it from that exact Figma node (`get_design_context`), not from a screenshot.
2. **Extract assets as SVG.** Icons and logos come out of the Figma REST API as real vectors (`format=svg`), not rasterized PNGs that blur on retina.
3. **Verify by measuring.** After building, read the rendered DOM's actual `getBoundingClientRect()` / `getComputedStyle()` and diff it against the Figma geometry. The diff *is* your fix list. Screenshots are for gross structure only.

The result: pages converge in 1–2 measure cycles instead of 5–8 eyeball-the-screenshot cycles, and they're right the first time far more often.

## Demo

> ▶️ **Before/after demo GIF coming soon.** (Figma frame on the left, the measured-and-matched live WordPress page on the right.)

```
  Figma node          read exact values         build on WP            measure back
 ┌───────────┐  ──▶  radius 8px, 13px,   ──▶  Elementor / Divi /  ──▶  DOM box + computed
 │  ▢  CTA   │       Medium, #307bff,         Gutenberg widget         styles  ==  Figma?
 └───────────┘       padding 9/25/9/23                                  └─ diff = fix list
        ▲                                                                        │
        └──────────────────────  re-measure until it matches  ◀─────────────────┘
```

## Quickstart (30 seconds)

```bash
# 1. install the skill
git clone https://github.com/MatasMartin/figma-to-wordpress.git
cp -r figma-to-wordpress ~/.claude/skills/figma-to-wordpress

# 2. set a read-only Figma token (Figma → Settings → Personal access tokens,
#    scopes: file_content:read + file_metadata:read)
export FIGMA_PAT="figd_your_token_here"
```

Then in Claude Code, point it at a Figma node and a target page:

> "Build the contact page from this Figma frame on my Elementor site: `https://figma.com/...?node-id=12-345`"

The skill activates on phrases like *"Figma to WordPress"*, *"implement Figma design"*, *"build WordPress page from Figma"*.

## What's inside

```
SKILL.md                     the method: never-guess rule, build loop, verification gates
references/
  figma-rest.md              Figma REST API — token setup, batch SVG/asset export
  environment.md             find your LocalWP socket, php/mysql/chromium paths
  gotchas-general.md         builder-agnostic traps (CSS specificity, migration, fonts, MIME)
  tool-reference.md          LocalWP, AIOWPM, Figma MCP+REST, Playwright, Safe SVG
  going-live.md              deploy a local build to a live host, end to end
  builders/
    elementor.md             where Elementor stores content + how to apply the method
    gutenberg.md             block markup + mapping Figma tokens → theme.json
    divi.md                  Divi shortcodes + drop-in templates
scripts/
  env-detect.sh              detect toolchain + the right LocalWP socket
  figma-geom.py              pull frame-relative geometry from Figma (for measuring)
  figma-build-diff.py        Figma-anchored layout diff (diagnostic)
  dom-measure.js             measure a rendered element vs its spec values
  overflow-sweep.js          multi-viewport horizontal-overflow gate
  lib-playwright.js          shared headless-Chromium helper (auto-installs)
```

## Requirements

- **Claude Code** (this is a skill for it).
- A **local WordPress** environment ([LocalWP](https://localwp.com/), DDEV, etc.) for the fast code-gen path. The manual builder path needs only wp-admin.
- **Figma** access + a read-only personal access token (`$FIGMA_PAT`). The official [Figma MCP](https://mcp.figma.com/mcp) is recommended for per-node specs.
- **Node** + **Python 3** for the verification scripts (`playwright-core` auto-installs on first run).

## Supported builders

Elementor (classic widgets), Gutenberg / Full Site Editing, and Divi 4 each have a dedicated note. The two core disciplines — never-guess and measure-to-verify — are builder-agnostic, so the method also works on Bricks, Oxygen, or a custom theme; only *where content is stored* changes.

## License

MIT © 2026 Matas Martinavičius
