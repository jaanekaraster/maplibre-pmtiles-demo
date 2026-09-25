# MapLibre + PMTiles Starter

A clean, commented `index.html` you can fork to start any web map. Used as the launchpad for Portfolio Project 3 (Live Web Map) in the Modern GIS Accelerator.

## What you get

- **`index.html`.** A complete working web map. Choropleth fill, click popups, legend, mobile-responsive layout. Heavily commented so you can read top-to-bottom and learn the pattern.
- **`examples/`.** Four standalone style patterns you can copy-paste from when you need a different layer type:
  - `choropleth.html`. Graduated color polygons (the default for thematic maps).
  - `categorical.html`. Discrete category fills (boroughs, land use, types).
  - `circles.html`. Proportional circles with both zoom and attribute scaling.
  - `lines.html`. Styled line layer with category color and zoom-responsive width.
- **No build step.** All dependencies load from CDN. Open `index.html` in a browser and it works.

## How to use it

1. **Fork or clone** this folder into a new repo named `your-project-name`.
2. **Generate your PMTiles file** with `tippecanoe` (see R3.3 for the flag reference).
3. **Replace `HYDRANT_DATA_URL`** in `index.html` with the URL where you'll host your `.pmtiles` file (typically a GitHub Pages URL).
4. **Update the layer's `source-layer` name** to match the layer name in your PMTiles (set by tippecanoe with `-l`).
5. **Edit the legend HTML** and the paint expression breaks to match your data range.
6. **Push to GitHub, enable Pages**, and your map is live (see R3.4 for the deployment checklist).

## How to test locally

You can open `index.html` directly in a browser, but PMTiles loaded from a remote URL needs to be served, not opened from `file://`. Run a tiny local server first:

```bash
# Python (built in)
python3 -m http.server 8000

# Or Node
npx http-server -p 8000
```

Then visit `http://localhost:8000` in your browser.

## What's in the bare `index.html`

Read top to bottom. Sections are commented:

1. **Head.** CDN imports for MapLibre and the PMTiles protocol handler. Inline CSS for the map container, legend, and mobile breakpoints.
2. **Body.** Title bar, legend card, and the `#map` div MapLibre renders into.
3. **Script step 1.** Register the PMTiles protocol with MapLibre.
4. **Script step 2.** Initialize the map. Default center is NYC. Replace with your area.
5. **Script step 3.** Add the data source and the styled layer when the basemap finishes loading.
6. **Script step 4.** Hover and click interactions for the popup.

## The mental model

Three concepts hold the whole library together:

- **Source.** Where the data lives. For PMTiles, it's the URL prefixed with `pmtiles://`.
- **Layer.** How a chunk of the source data is drawn. One source can feed many layers. Layer types: `fill`, `line`, `circle`, `symbol`, `raster`.
- **Style (paint).** The visual properties of a layer. Expressed as expressions like `["step", ["get", "column"], color, break, color, ...]`.

If you internalize source → layer → style, the rest of MapLibre is mostly looking up the right paint expression.

## Common gotchas

**Map is blank.** Open browser dev tools (F12). Console errors usually tell you exactly what's wrong. Most common: a missing comma in the style expression.

**PMTiles URL doesn't load.** Check three things: (1) the URL is reachable in a browser by itself, (2) the host serves it with proper CORS headers (GitHub Pages does by default), (3) you've prefixed it with `pmtiles://`.

**`source-layer` not found.** The layer name inside the PMTiles file is set by `tippecanoe -l <name>`. Default is the input filename. Open the PMTiles file with `pmtiles show <file>` to confirm.

**Popup shows `undefined`.** The property name in your code doesn't match what's in the tile. Use `console.log(e.features[0].properties)` inside the click handler to see what's actually there.

## What this is preparing you for

This template is the launchpad for PP3. After you finish PP3, the same `index.html` pattern carries forward to:

- The capstone project in Part 4 (Overture-scale data via DuckDB → PMTiles → MapLibre).
- Any future client work where you need a public-facing web map.
- Most modern web mapping job interviews. The "explain how MapLibre works" question becomes a 60-second answer once you've published this once.

## License

MIT. Fork freely.
