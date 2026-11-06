# GradeScout

A single-file web app for reading free USGS 3DEP lidar on farm ground: drainage paths, ponding, slope classes, soils, cut/fill grade design, elevation profiles, 3D rain with NOAA Atlas 14 design storms, landowner parcels, crop-health imagery, and pins/tracks/GPS for scouting.

Live at **https://bradyontech.github.io/gradescout/**

## Run it

Open `index.html` in a browser. There's no build step. It needs an internet connection, because all map data comes live from public servers (USGS, USDA, NOAA, Esri, state GIS offices).

GPS and share links need the file hosted over `https://`. The live copy is on GitHub Pages, and Netlify Drop or Cloudflare Pages work too.

## Guided tour

The tour starts on the first visit, and the **Tour** button at the bottom of the icon bar replays it. Its steps and the sample fields (the `SAMPLES` list, with a Wisconsin ridge-top field and Minnesota vegetable plots) are near the bottom of `index.html`, in the "guided tour" section.

## Where the data comes from

Every layer is fetched straight from the publisher's public service when you look at it. GradeScout has no server of its own and keeps no copy of the data. Each publisher sets its own availability and terms of use.

### Elevation and lidar

| Data | Source | Used for |
| --- | --- | --- |
| Bare-earth elevation | [USGS 3D Elevation Program (3DEP)](https://www.usgs.gov/3d-elevation-program), through its dynamic elevation ImageServer | Field analysis, profiles, 3D rain, the hillshade and slope maps, and the elevation shown for pins and GPS |
| 2 ft contours | The same 3DEP ImageServer, drawn with its 2 ft contour preset | The contour switch in Map |
| Lidar survey dates and quality | USGS 3DEP Elevation Index | The badge showing when the lidar was flown and its quality level (QL1, QL2) |

### Imagery and base maps

| Data | Source | Used for |
| --- | --- | --- |
| Satellite imagery | Esri World Imagery (Maxar, Earthstar Geographics) | Default base map and the maps in the PDF report |
| Historical imagery | Esri World Imagery Wayback | Older photos, picked by release date |
| Recent satellite and crop health | Copernicus Sentinel-2, through Esri's Sentinel-2 imagery service | Recent passes as true colour, crop health (NDVI), colour infrared, moisture (NDMI) and agriculture bands |
| Aerial photos and topo maps | USGS The National Map (NAIP imagery, US Topo) | Alternative base maps |
| Topographic map | Esri World Topographic Map | Alternative base map |
| Street map | OpenStreetMap contributors | Alternative base map |
| Roads and place labels | Esri reference layers | The "Roads and labels" switch |

### Land, soil and water layers

| Data | Source | Used for |
| --- | --- | --- |
| Soils | USDA NRCS Soil Survey (SSURGO), through Soil Data Access | The soil layer, the soil list for a field (drainage class, hydrologic group, capability class) and click-to-identify |
| Landowner parcels | State GIS offices for Arkansas, Iowa (2017 data), Minnesota, Mississippi, Montana, Nebraska, North Carolina, Ohio, Texas and Wisconsin | Parcel outlines, owner and parcel ID on click, and "Analyse this parcel as a field" |
| Public land | USGS Protected Areas Database of the US (PAD-US) | The public land layer |
| Flood zones | FEMA National Flood Hazard Layer | The flood zone layer and identify |
| Wetlands | US Fish and Wildlife Service National Wetlands Inventory | The wetlands layer and identify |
| Streams and water bodies | USGS National Hydrography Dataset (NHD) | The streams and water layer |
| Sections and townships | BLM Public Land Survey System (PLSS) | The section, township and range layer and identify |
| Crop type by year | USDA NASS Cropland Data Layer (CropScape) | The crop type layer, with a year picker |

### Rainfall and search

| Data | Source | Used for |
| --- | --- | --- |
| Design storms | NOAA Atlas 14 precipitation frequency estimates, from NOAA's Precipitation Frequency Data Server | The 2-year to 100-year storms in 3D rain, for the field's exact location |
| Place search | OpenStreetMap Nominatim | Finding a town, road or address (US only) |

The "Open this spot in" links (Google Maps, Google Earth, Bing Maps, OpenStreetMap, USGS TopoView) only open those sites at the same location.

## What it runs on

**One HTML file.** The whole app (markup, styles and code) is `index.html`, written in plain JavaScript as an ES module. There's no framework, bundler, package manager or build step.

**Libraries**, loaded from the jsDelivr CDN at fixed versions:

| Library | Version | Used for |
| --- | --- | --- |
| [Leaflet](https://leafletjs.com/) | 1.9.4 | The map, tile layers, drawing and pop-ups |
| [three.js](https://threejs.org/) | 0.170.0 | The 3D rain view (WebGL), with OrbitControls for the camera |
| [LERC decoder](https://github.com/Esri/lerc) | 2.0.0 | Decoding the compressed elevation rasters from 3DEP |
| [jsPDF](https://github.com/parallax/jsPDF) | 2.5.2 | The PDF field report |
| [JSZip](https://stuk.github.io/jszip/) | 3.10.1 | Reading KMZ files and zipped shapefiles, and writing the shapefile export |
| [shpjs](https://github.com/calvinmetcalf/shapefile-js) | 6.1.0 | Reading zipped shapefiles |

**Analysis written for this project**, running in the browser:

- **Drainage:** priority-flood depression filling, then D8 flow directions and flow accumulation. This produces the drainage paths, outlet acres and ponding depths.
- **Grade design:** a least-squares best-fit plane for each section, raised or lowered until cut and fill balance at the chosen ratio.
- **Contours, slope classes and profiles:** contours use marching squares on the field grid.
- **3D rain:** a cell-by-cell water model. Each step, rain lands on every cell, part of it soaks in at the chosen rate, and the rest flows downhill.
- **Files:** import and export for KML/KMZ, GPX, GeoJSON and shapefiles, plus CSV and PDF reports.

**Browser features:**
- Canvas 2D for overlays and charts.
- WebGL for the 3D view.
- The Geolocation API for "My location" and track recording.
- The File API for imports.
- `localStorage` for saved fields, pins, tracks and the tour's "seen" flag.

Nothing you draw or save leaves your browser unless you export or share it. The exception is the coordinates and search terms that the map services above need in order to answer a request.

**Hosting:** GitHub Pages serves `index.html` from the `main` branch, so pushing to `main` updates the live site.
