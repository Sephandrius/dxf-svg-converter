# Vector Exchange — SVG ⇄ DXF converter

A single-file, fully client-side converter between SVG and DXF. No server, no upload — files are converted in the browser, which makes it perfect for GitHub Pages. Built with laser-cutter workflows in mind.

## Deploy to GitHub Pages

1. Create a new repository (e.g. `svg-dxf`).
2. Add `index.html` to the repository root and push.
3. In the repo: **Settings → Pages → Source: Deploy from a branch**, pick `main` and `/ (root)`, save.
4. Your tool is live at `https://<your-username>.github.io/svg-dxf/` within a minute or two.

That's the whole deployment — there is no build step and no dependencies beyond two Google Fonts (the tool still works if fonts fail to load).

## What it does

**SVG → DXF (R12)**
- Parses `path` (all commands: M L H V C S Q T A Z, absolute and relative), `rect` (including rounded corners), `circle`, `ellipse`, `line`, `polyline`, `polygon`
- Resolves nested `transform`s (matrix, translate, scale, rotate, skewX/Y)
- Flattens béziers and elliptical arcs to polylines at an adjustable tolerance (default 0.1 mm)
- Keeps untransformed/uniformly-scaled circles as true DXF `CIRCLE` entities
- Reads physical units from `width`/`viewBox` automatically (e.g. Inkscape mm documents); manual override available
- Groups output into DXF layers by stroke color, mapped to the nearest AutoCAD color — red cut lines stay red
- Writes DXF R12 (`POLYLINE`/`VERTEX` + `CIRCLE` + `LINE`), the most widely compatible flavor — opens in LightBurn, AutoCAD, Fusion 360, LibreCAD, etc. Output is always millimeters.

**DXF → SVG**
- Entities: `LINE`, `CIRCLE`, `ARC`, `LWPOLYLINE` (with bulges), `POLYLINE`/`VERTEX`, `ELLIPSE`, `SPLINE` (full NURBS evaluation)
- Bulges and arcs are written as true SVG arcs — no flattening in this direction
- Reads `$INSUNITS` from the header for correct physical sizing (mm assumed when unitless)
- AutoCAD colors map to SVG strokes

Both directions show a geometry preview, dimensions, and a conversion log (including anything skipped) before you download.

## Known limitations

- SVG `<text>`, `<image>`, and `<use>` elements are skipped (counted in the log). Convert text to paths in your design tool first.
- DXF `INSERT`/block references, `HATCH`, and `TEXT`/`MTEXT` are skipped (counted in the log). Explode blocks before exporting if needed.
- DXF output flattens curves to polylines — standard practice for laser toolpaths; tighten the smoothness slider if facets are visible at your scale.
