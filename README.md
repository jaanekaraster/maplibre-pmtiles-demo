## NYC Hydrant Density Map

An interactive map visualizing hydrant density across New York City neighborhoods, built with MapLibre GL JS and PMTiles.

The project demonstrates a lightweight, browser-friendly workflow for converting geospatial data into vector tiles and building multiple visualizations from the same dataset.


### Project Goal

This project explores how a relatively simple geospatial dataset can be transformed into an efficient, interactive visualization using an entirely static web-mapping workflow.

### Tools

- MapLibre GL JS: Interactive web mapping
- PMTiles: Single-file vector tile storage
- Tippecanoe: GeoJSON → vector tile conversion
- GDAL/OGR: geometry transformation
- NYC Open Data: borough boundary data

### What the Map Shows

- NYC borough boundaries for geographic context
- Hydrant density by neighborhood, measured as hydrants per km²
- A polygon visualization showing neighborhood-level density.
- A circle visualization where each neighborhood is represented by a circle sized by hydrant density
- Neighborhood boundaries are displayed as contextual outlines when using the circle visualization

### Data Sources

**NYC Borough Boundaries**
Source: NYC Open Data: Borough Boundaries

The borough boundaries were downloaded as GeoJSON and converted to PMTiles using Tippecanoe:

```
tippecanoe \
  -o neighborhoods.pmtiles \
  --drop-densest-as-needed \
  --coalesce-densest-as-needed \
  -z 12 \
  -Z 6 \
  -l neighborhoods \
  --force \
  Borough_Boundaries_20260925.geojson
```

**Neighborhood Hydrant Density**

The neighborhood hydrant-density dataset was created in a previous exercise and is included in this repository as GeoJSON.

The dataset contains attributes including:

```
ntaname: neighborhood name
boroname: borough
hydrant_count: number of hydrants
area_km2: neighborhood area
hydrants_per_km2: hydrant density
```

**Circle Visualization**

To create the circle visualization, the neighborhood polygons were converted to representative points using GDAL/OGR.

ST_PointOnSurface() was used so that each point remains within its corresponding neighborhood polygon:

```
ogr2ogr \
  -dialect sqlite \
  -sql "SELECT ST_PointOnSurface(geometry) AS geometry, * FROM neighborhood_density" \
  neighborhood_density_points.geojson \
  neighborhood_density.geojson
```

The resulting point dataset contains one point per neighborhood polygon while retaining the density attributes used to size the circles.

**PMTiles**

The polygon and point datasets are stored as separate vector-tile layers within a single PMTiles archive:

```
tippecanoe \
  -o neighborhood_density.pmtiles \
  --force \
  --no-feature-limit \
  --no-tile-size-limit \
  -z 12 \
  -Z 6 \
  -L hydrant_density_polygons:neighborhood_density.geojson \
  -L hydrant_density_points:neighborhood_density_points.geojson
```

This allows MapLibre to use a single PMTiles source while rendering the polygon and point layers independently.

### Visualization

The map is built with MapLibre GL JS, using the PMTiles protocol to load vector tiles directly in the browser.

The polygon layer uses a fill visualization, while the point layer uses a circle visualization. Circle radius is driven by the hydrants_per_km2 attribute, allowing the map to communicate density through both geographic area and proportional symbol size.

Using PMTiles keeps the map architecture simple: the data is packaged into a single static tile archive that can be served alongside the web application without requiring a traditional tile server.
