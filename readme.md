# Digitizing data from georeferenced historical maps on the web

# Introduction

In this workshop, you'll learn how to use a georeferenced historic map as a spatial data source by digitizing features from it using simple web-based tools. Our focus will be on Dr. John Snow’s iconic 1854 map of cholera outbreaks in London—a foundational example of spatial thinking in public health. By the end of the session, you’ll understand how to load a historic map as a raster tile layer, identify spatial features (in this case, water pumps), and digitize those features into a GeoJSON file. This process allows researchers, students, and digital humanists to transform static visual information from historic maps into spatial data that can be analyzed, shared, and layered with modern geographic information.

![John Snow's 1854 Cholera Map - IIIF Image](https://iiif.archive.org/iiif/snow_map_modified/full/1140,/0/default.jpg)

## Data We Will Use

* **Archive.org Details Page for Snow Map**

  [https://archive.org/details/snow_map_modified](https://archive.org/details/snow_map_modified)

  This is the map's main page on Archive.org, where you can view the uploaded image, download files, and access metadata.
* **John Snow’s Cholera Map (IIIF Image Info JSON)**

  `https://iiif.archive.org/iiif/snow_map_modified/info.json`

  This URL points to the IIIF manifest for the map, which makes it accessible to IIIF-compatible viewers and georeferencing tools. You can learn how to [craft a IIIF manifest URL, here](https://training.iiif.io/time_machine/day-two/image-servers/iiif-hosting-ia.html).
* **Georeferenced Version (Allmaps)**

  `https://editor.allmaps.org/results?url=https%3A%2F%2Fiiif.archive.org%2Fiiif%2Fsnow_map_modified%2Finfo.json&image=https%3A%2F%2Fiiif.archive.org%2Fimage%2Fiiif%2F2%2Fsnow_map_modified%252fsnow_map_modified.tif`

  This link opens the map in Allmaps Editor with the applied georeferencing control points.
* **XYZ Tile Service URL (for use in geojson.io)**

  `https://allmaps.xyz/images/45bbc7ea6e45e5c9/{z}/{x}/{y}.png`

  This is the XYZ tile service URL that allows you to use the georeferenced map as a background layer in geojson.io or any other tool that supports raster tile services.

## Preparation Required

Before the workshop, participants should have completed the two preceding instructor-led workshops in the Bootcamp Series, or at a minimum have familiarized themselves with the following:

1. **Complete the IIIF Map Hosting Tutorial**
   Follow the instructions at:
   `https://training.iiif.io/time_machine/day-two/image-servers/iiif-hosting-ia.html`
   This covers how to upload and publish a IIIF manifest using the Internet Archive.
2. **Complete the Allmaps Georeferencing Tutorial**
   Visit:
   `https://allmaps.org/#getting-started/`
   Learn how to load a IIIF manifest and create a set of control points to georeference the map.

---

## Tools and Website Infrastructure We Will Use
* **[geojson.io](https://geojson.io/)**
  A simple, browser-based web application for editing and visualizing GeoJSON files. We'll use the **“Add raster tile layer”** feature to bring in the John Snow map and digitize water pump locations with their associated street labels.
* **[Allmaps Editor](https://editor.allmaps.org/)**
  Used to georeference the IIIF map. This tool allows you to match control points from a historic map to modern locations.
* **[Allmaps Tile Server](https://allmaps.xyz/)**
  This generates an XYZ tile service from your georeferenced map, allowing the map to be used as a basemap in tools like geojson.io.
* **[Archive.org](https://archive.org/)**
  A public digital library where we upload and host the IIIF version of the historic map. It provides permanent URLs and integrates with the Allmaps georeferencing tools.
* **[IIIF Hosting Instructions (Training Site)](https://training.iiif.io/time_machine/day-two/image-servers/iiif-hosting-ia.html)**
  This instructional guide walks you through the steps of uploading a historic map image to Archive.org and generating a IIIF manifest, a key step in making your map usable in geospatial workflows.
* **[GitHub](https://github.com/)**
  A platform for hosting, sharing, and collaborating on code and documentation. Workshop materials and example datasets are provided via GitHub repositories.
* **[Leaflet.js](https://leafletjs.com/)**
  An open-source JavaScript library for interactive maps. Leaflet.js is widely used for displaying geospatial data and custom map layers in web applications.
* **[Turf.js](https://turfjs.org/)**
  A JavaScript library for advanced geospatial analysis. Turf.js enables spatial operations such as measuring distances, buffering, and working with GeoJSON data directly in the browser.

