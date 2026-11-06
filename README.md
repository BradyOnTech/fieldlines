# GradeScout

A single-file web app for reading free USGS 3DEP lidar on farm ground: drainage paths, ponding, slope classes, soils, cut/fill grade design, elevation profiles, 3D rain with NOAA Atlas 14 design storms, landowner parcels, crop-health imagery, and pins/tracks/GPS for scouting.

## Run it

Open `index.html` in a browser. There's no build step. It needs an internet connection, because all map data comes live from public servers (USGS, USDA, NOAA, Esri, state GIS offices).

GPS and share links need the file hosted over `https://`, for example on Netlify Drop, GitHub Pages or Cloudflare Pages.

## Guided tour

The tour starts on the first visit, and the **Tour** button at the bottom of the icon bar replays it. Its steps and the sample fields (the `SAMPLES` list, with a Wisconsin ridge-top field and Minnesota vegetable plots) are near the bottom of `index.html`, in the "guided tour" section.
