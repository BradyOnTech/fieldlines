# FieldLines

A single-file web app for reading free USGS 3DEP lidar on farm ground: drainage paths, ponding, slope classes, soils, several fields analysed together, cut/fill grade design with ditches and a staking cut sheet, elevation profiles, NOAA Atlas 14 design storms played on the map or in 3D (today's ground and your design side by side), where a spot's water comes from, food-farm records (beds and blocks, a harvest log with lot codes, soil-test zones), landowner parcels, crop-health imagery, and pins/tracks/GPS for scouting. US or metric units.

Live at **https://bradyontech.github.io/fieldlines/**

## Run it

Open `index.html` in a browser. There's no build step. It needs an internet connection, because all map data comes live from public servers (USGS, USDA, NOAA, Esri, state GIS offices).

GPS and share links need the file hosted over `https://`. The live copy is on GitHub Pages, and Netlify Drop or Cloudflare Pages work too.

## Guided tour

The tour starts on the first visit, and the **Tour** button at the bottom of the icon bar replays it. Its steps and the sample fields (the `SAMPLES` list, with a Wisconsin ridge-top field and Minnesota vegetable plots) are near the bottom of `index.html`, in the "guided tour" section.

## Where the data comes from

Every layer is fetched straight from the publisher's public service when you look at it. FieldLines has no server of its own and keeps no copy of the data. Each publisher sets its own availability and terms of use.

### Elevation and lidar

| Data | Source | Used for |
| --- | --- | --- |
| Bare-earth elevation | [USGS 3D Elevation Program (3DEP)](https://www.usgs.gov/3d-elevation-program), through its dynamic elevation ImageServer | Field analysis, profiles, 3D rain, grade stakes, the detailed lidar styles and contours (raw elevation, drawn in the browser), the ground height under the cursor, and the elevation shown for pins and GPS |
| Quick hillshades and 2 ft contours | The same 3DEP ImageServer, drawn by the server with its hillshade and 2 ft contour presets | The default lidar styles in Map (small downloads) |
| Lidar survey dates and quality | USGS 3DEP Elevation Index | The badge showing when the lidar was flown and its quality level (QL1, QL2) |

### Imagery and base maps

| Data | Source | Used for |
| --- | --- | --- |
| Satellite imagery | Esri World Imagery (Maxar, Earthstar Geographics) | Default base map, the 3D ground picture and the maps in the PDF report. Where a close-up photo is missing, Esri's tile map is checked and the nearest coarser photo is enlarged instead of a blank tile |
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
| [three.js](https://threejs.org/) | 0.170.0 | The 3D rain view (WebGL), with OrbitControls for the camera. Loaded the first time 3D opens |
| [LERC decoder](https://github.com/Esri/lerc) | 2.0.0 | Decoding the compressed elevation rasters from 3DEP |
| [jsPDF](https://github.com/parallax/jsPDF) | 2.5.2 | The PDF field report. Loaded once a field is on screen |
| [JSZip](https://stuk.github.io/jszip/) | 3.10.1 | Reading KMZ files and zipped shapefiles, and writing the shapefile export. Loaded when needed |
| [shpjs](https://github.com/calvinmetcalf/shapefile-js) | 6.1.0 | Reading zipped shapefiles. Loaded when needed |

**Analysis written for this project**, running in the browser:

- **Drainage:** priority-flood depression filling, then D8 flow directions and flow accumulation. This produces the drainage paths, outlet acres and ponding depths.
- **Grade design:** a least-squares best-fit plane for each section, raised or lowered until cut and fill balance at the chosen ratio. Fall arrows show each section's slope and direction, and every design change can be undone or redone.
- **Several fields at once:** fields share one analysis window, and every cell knows which field it's in. Drainage, storms and 3D see water move between fields; sections, outlets, soils and a comparison list stay per field. Share links, saved fields, exports (one shapefile record per field) and the PDF carry all of them.
- **Ditches:** a trapezoid channel (depth at its top, grade, bottom width, side slope) cut into the design surface, and into today's ground outside the field, so it shows in cut/fill, contours, profiles, stakes, storms and 3D. Each reports its digging volume, and warns if its bottom would come out of the ground or end below its outlet.
- **Grade stakes:** a north-up grid over the field (rows A, B, C… north to south, columns 1, 2, 3… west to east) with the cut or fill at each stake from the section's plane and the smoothed lidar. They appear as a cut sheet in the PDF report (map plus full table), on the map with GPS guidance to the nearest stake, and as CSV or GPX files.
- **Contours, slope classes and profiles:** field contours use marching squares on the field grid. Slope classes average the ground over about 10 m first, so lidar noise and crop rows don't read as slope.
- **Detailed lidar styles:** raw elevation tiles are shaded in the browser: your own sun direction and height, height stretch and shadows; elevation colours stretched to the ground on screen; slope classes; and contours at any interval. The quick server hillshades stay the default because raw tiles are about ten times bigger.
- **3D rain:** shallow-water flow using the local inertial equations (Bates, Horritt & Fewtrell 2010) with Manning friction, so the clock is real storm time and speeds are physical. Rain falls on today's ground or on the design surface; the field edge can be natural, drain freely, or be bermed. Flow streaks show where and how fast water runs, and the stats balance rain against water on the field, soak-in and runoff.
- **Storm on the map:** the same shallow-water model run on the 2D map with depth classes, a clock, a time slider and the deepest water reached; the deepest-water map goes in the PDF. Soak-in starts from the soil survey's hydrologic groups (NRCS minimum infiltration rates).
- **Where does this water come from?** A separate, wider lidar grid (about 2 km around a spot, or 800 m around the fields) is depression-filled and routed, then every cell upstream of the spot (or draining onto the fields) is shaded. Pins marked as manure, compost or livestock inside the shaded area are flagged.
- **Records (beds, blocks, harvests, soil tests):** blocks drawn by hand or laid out along the contour of an analysed field. Each block reads its ground from the lidar (best-fit slope, ponding share, a topographic wetness index, mean height, dominant soil). The harvest log gives every entry a lot code and exports as CSV. Yield by block compares the top and bottom thirds of blocks on whichever ground property lines up best with yield. Soil sampling zones are k-means clusters of height, wetness and slope within the field, smoothed, with core spots spread through each zone's interior; lab results can be typed in or imported from the lab's CSV.
- **Units:** everything is computed in metres; US (ft, ac, yd³, in) or metric (m, ha, m³, mm) only changes what's shown and what's written to files.
- **Files:** import and export for KML/KMZ, GPX, GeoJSON and shapefiles, plus CSV and PDF reports.

**Browser features:**
- Canvas 2D for overlays and charts.
- WebGL for the 3D view.
- The Geolocation API for "My location" and track recording.
- The File API for imports.
- `localStorage` for saved fields, pins, tracks, blocks, harvests, soil plans, your units and lidar style, and the tour's "seen" flag.

Nothing you draw or save leaves your browser unless you export or share it. The exception is the coordinates and search terms that the map services above need in order to answer a request.

**Hosting:** GitHub Pages serves `index.html` from the `main` branch, so pushing to `main` updates the live site.
