# Madagascar Plague Risk Mapping

Plague continues to cause deadly outbreaks in Madagascar, yet health officials lack detailed maps showing where disease risk is highest. This detailed community-level risk assessment enables health authorities to target disease surveillance, prevention efforts, and resources to specific locations where plague is most likely to occur, potentially saving lives through earlier detection and response to outbreaks. This interactive web-based mapping application is designed to visualizing plague ecology, transmission risk, and species distribution data across Madagascar. Built with Leaflet.js, the application renders GeoTIFF raster layers directly in the browser and overlays vector data from GeoJSON sources, providing a comprehensive spatial decision-support tool for plague surveillance and research.

# Citation
Deka, M. A. (2026). A High-Resolution Geospatial Framework for Zoonotic Plague Risk Mapping in Madagascar. Zoonoses, 6(1).

# Explore the interactive map: [https://cchfvirus.github.io/Madagascar-Plague-Risk-Mapping/](https://geospatialhealth.github.io/Madagascar-Plague-Risk-Mapping/)

---

## Overview

This single-page HTML application loads and displays multiple geospatial datasets related to plague risk in Madagascar's central highlands (centered on the Antananarivo region). Users can toggle layers on and off, adjust opacity, query raster cell values, and interact with point and polygon features through popups. The map is designed for researchers, epidemiologists, and public health professionals working on plague ecology and risk assessment.

---

## Features

### **Basemaps**

Two Esri basemaps are available, selectable via radio buttons in the layer control panel:

- **Satellite Imagery** (default) – Esri World Imagery for high-resolution context
- **Street Map** – Esri World Street Map for administrative and road reference

### **Risk Layers (Raster)**

Four GeoTIFF raster layers are loaded and rendered client-side using the GeoRaster and GeoRasterLayer libraries. Each layer occupies a dedicated map pane for proper z-index stacking.

- **Ecological Niche Model (1km)** – Continuous habitat suitability surface (0–1) displayed as a smooth color gradient from grey (low suitability) through yellow and orange to dark red (high suitability). Rendered using a Chroma.js LCH color scale. Displayed by default on map load.
- **Plague Exposure Risk Index** – Discrete risk classification (7 classes: 0–9) representing plague transmission risk zones. Each class is individually toggleable in the legend. Values include No Risk (0), Very Low (1), Low (2), Moderate (3), Elevated (4), High (6), and Very High (9).
- **Elevation (DEM)** – Digital elevation model classified into 5 elevation bands using Natural Breaks: 0–226 m, 226–560 m, 560–916 m, 916–1,314 m, and 1,314–2,747 m. Rendered on a separate background raster pane so it sits beneath analysis layers.
- **Species Richness** – Number of species per grid cell, classified into 5 breaks (1–3, 4–10, 11–23, 24–45, 46–165 species) using a sequential green color scheme.

### **Hot Spot Analysis (Commune-Level Polygons)**

A commune-level (admin4) polygon layer displaying results of Getis-Ord Gi* hot spot analysis. Two display modes are available, switchable via radio buttons:

- **Gi\* Cluster** – Seven-class diverging scheme from hot spots (99%, 95%, 90% confidence) through not significant to cold spots (90%, 95%, 99% confidence)
- **Weighted Risk** – Nine-class scheme from very low (0–1) to extreme+ (8–9) population-weighted risk

Each class in either mode is independently toggleable via legend checkboxes, allowing users to isolate specific confidence levels or risk tiers.

### **Species & Survey Data (Vector)**

Four GeoJSON point layers with distinct marker symbology for visual differentiation:

| Layer | Symbol | Color |
|-------|--------|-------|
| GBIF Species Occurrences | Circle | Colored by plague ecology **Role** |
| Plague Survey – Ankazobe | Diamond | Amber |
| Plague Flea Survey | Triangle | Green |
| Plague Survey – Makira | Square | Pink |

All species and survey markers use black outlines for visibility against the satellite basemap.

**GBIF Role-Based Symbology** – When the GBIF layer is activated, species occurrences are automatically colored by their `Role` property (e.g., Reservoir, Host, Vector, Predator, Prey, Carrier, Sentinel, Commensal). Roles are extracted dynamically from the data on load, sorted by ecological significance, and each role is individually toggleable in the legend with feature counts displayed. The color palette is designed for plague ecology context:

| Role | Color | Ecological Significance |
|------|-------|------------------------|
| Reservoir | Red | Primary reservoir hosts |
| Host | Orange | General hosts |
| Vector | Purple | Flea vectors |
| Predator | Blue | Predators in food web |
| Prey | Light Green | Prey species |
| Carrier | Pink | Mechanical carriers |
| Sentinel | Green | Indicator species |
| Commensal | Light Orange | Commensal species |

### **Overlay Layers**

- **Plague Cases (1939–2023)** – Historical plague occurrence records displayed as white target/bullseye markers with black concentric rings. Each point is clickable to reveal case attributes in a popup. Displayed by default.
- **Madagascar ADMIN2 Boundaries** – District-level administrative boundaries rendered as outline-only polygons. Stroke color automatically adapts (white when the risk layer is active, black otherwise) for contrast.

### **Interactive Legend**

A dynamic legend (bottom-right) updates automatically based on active layers. Key behaviors:

- Continuous gradient bar for the ENM layer
- Toggleable checkboxes for discrete/classified layers (Risk Index, Elevation, Species Richness, Hot Spot classes, GBIF Roles) allowing users to show/hide individual classes directly from the legend
- Hidden classes appear with strikethrough styling and reduced opacity
- Matching symbology for all vector layers (circles, diamonds, triangles, squares)

### **Layer Opacity Controls**

Individual opacity sliders (0–100%) for every layer, located in the layer control panel under "Layer Opacity." Adjustments apply in real time.

### **Raster Cell Query (Click)**

Left-clicking the map displays a popup with the active raster layer's cell value at that location, including coordinates (6 decimal places) and the value with appropriate formatting (discrete integer for risk, decimal for ENM/elevation, meters suffix for DEM).

### **Coordinate Copy (Right-Click)**

Right-clicking anywhere on the map copies the coordinates (latitude, longitude) to the clipboard with a brief on-screen notification. Includes a fallback method for browsers without Clipboard API support.

### **Measurement Tools**

Drawing tools (top-right, via Leaflet Draw) for on-map measurement:

- **Polyline** – Measure distance in kilometers between drawn vertices
- **Polygon** – Measure geodesic area in km²
- **Rectangle** – Estimate rectangular area in km²

Drawn shapes persist on the map with measurement popups and can be edited or deleted.

---

## Data Files

The application expects the following files in the same directory as `index.html`:

| File | Type | Description |
|------|------|-------------|
| `enm_cog.tif` | Cloud-Optimized GeoTIFF | Ecological niche model suitability (0–1) |
| `risk_zones_cog.tif` | Cloud-Optimized GeoTIFF | Plague exposure risk index (discrete classes) |
| `elevation.tif` | GeoTIFF | Digital elevation model (meters) |
| `species_richness.tif` | GeoTIFF | Species richness per grid cell |
| `cases.geojson` | GeoJSON (Point) | Historical plague cases (1939–2023) |
| `admin2.json` | GeoJSON (Polygon) | Madagascar ADMIN2 district boundaries |
| `hotspot_admin4.json` | GeoJSON (Polygon) | Commune-level Gi* hot spot analysis results |
| `GBIF_species.geojson` | GeoJSON (Point) | GBIF species occurrences with `Role` attribute |
| `plague_ankazobe_survey.geojson` | GeoJSON (Point) | Plague survey data – Ankazobe district |
| `plague_flea_survey.geojson` | GeoJSON (Point) | Plague flea survey data |
| `plague_makira_survey.geojson` | GeoJSON (Point) | Plague survey data – Makira region |

---

## Dependencies

All libraries are loaded from CDNs — no build step or package installation is required.

| Library | Version | Purpose |
|---------|---------|---------|
| [Leaflet](https://leafletjs.com/) | 1.9.4 | Map framework |
| [GeoRaster](https://github.com/GeoTIFF/georaster) | 1.6.0 | GeoTIFF parsing in the browser |
| [GeoRasterLayer for Leaflet](https://github.com/GeoTIFF/georaster-layer-for-leaflet) | 3.10.0 | Rendering parsed GeoTIFFs on Leaflet maps |
| [Chroma.js](https://gka.github.io/chroma.js/) | 2.4.2 | Color scale generation for continuous rasters |
| [Leaflet Draw](https://leaflet.github.io/Leaflet.draw/) | 1.0.4 | Drawing and measurement tools |

---

## Getting Started

The application must be served through a web server (file:// protocol will trigger CORS errors when loading GeoTIFF and GeoJSON files).

**Python:**

```bash
cd /path/to/project
python -m http.server 8000
```

**R:**

```r
servr::httd()
```

Then open `http://localhost:8000` in a browser.

---

## Map Pane Architecture

Layers are rendered across custom Leaflet panes to ensure correct visual stacking:

| Pane | z-index | Contents |
|------|---------|----------|
| `backgroundPane` | 250 | Reserved |
| `rasterPane` | 300 | Elevation DEM |
| `analysisPane` | 310 | ENM, Risk Index, Hot Spot polygons |
| `speciesRasterPane` | 315 | Species Richness raster |
| `boundariesPane` | 350 | ADMIN2 boundary outlines |
| `speciesVectorPane` | 360 | GBIF, plague survey point markers |

This ensures that vector points always appear above raster layers, boundaries overlay analysis surfaces, and the elevation basemap sits beneath all analytical layers.

---

## License

This project is provided for research and public health purposes. Data sources should be cited according to their respective licenses (GBIF, OpenStreetMap, Esri, GADM).

