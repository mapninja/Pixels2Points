# Digitizing data from georeferenced historical maps on the web

Published at: [https://mapninja.github.io/Pixels2Points/](https://mapninja.github.io/Pixels2Points/)

Accompanying Slides: [https://slides.com/staceymaples/digitizingbootcamp](https://slides.com/staceymaples/digitizingbootcamp)

# Introduction

Here, you'll learn how to use a georeferenced historic map as a spatial data source by digitizing features from it using simple web-based tools. Our focus will be on Dr. John Snow’s iconic 1854 map of cholera outbreaks in London—a foundational example of spatial thinking in public health.

By the end of the session, you’ll understand how to:

1. Load an historic map as a raster tile layer in geojson.io,
2. Identify spatial features (in this case, water pumps),
3. Digitize those features into a GeoJSON file, with properties.

This process allows researchers, students, and digital humanists to transform static visual information from historic maps into spatial data that can be analyzed, shared, and layered with modern geographic information.

![John Snow's 1854 Cholera Map - IIIF Image](https://iiif.archive.org/iiif/snow_map_modified/full/1140,/0/default.jpg)

## Data We Will Use

* **Archive.org Details Page for Snow Map**

  [https://archive.org/details/snow_map_modified](https://archive.org/details/snow_map_modified)

  This is the map's main page on Archive.org, where you can view the uploaded image, download files, and access metadata.
* **John Snow’s Cholera Map (IIIF Image Info JSON)**

  `https://iiif.archive.org/iiif/snow_map_modified/info.json`

  This URL points to the IIIF manifest for the map, which makes it accessible to IIIF-compatible viewers and georeferencing tools. You can learn how to [craft a IIIF manifest URL for Internet Archive images, here](https://training.iiif.io/time_machine/day-two/image-servers/iiif-hosting-ia.html).
* **Georeferenced Version (Allmaps)**

  `https://editor.allmaps.org/results?url=https%3A%2F%2Fiiif.archive.org%2Fiiif%2Fsnow_map_modified%2Finfo.json&image=https%3A%2F%2Fiiif.archive.org%2Fimage%2Fiiif%2F2%2Fsnow_map_modified%252fsnow_map_modified.tif`

  This link opens the map in Allmaps Editor with the applied georeferencing control points.
* **XYZ Tile Service URL (for use in geojson.io)**

  `https://allmaps.xyz/images/45bbc7ea6e45e5c9/{z}/{x}/{y}.png`

  This is the XYZ tile service URL that allows you to use the georeferenced map as a background layer in geojson.io or any other tool that supports raster tile services.

## What's this IIIF stuff?

The **[International Image Interoperability Framework (IIIF)](https://iiif.io)** is a standard for delivering high-resolution images and metadata over the web. It was developed by libraries, archives, and museums to make digitized materials — like manuscripts, paintings, and maps — easier to share and interact with. IIIF is especially valuable for working with historical maps because it supports **deep zooming** into fine details, allows consistent access to image tiles without downloading massive files, and is widely used by institutions hosting large digital collections. Most importantly for this workshop, IIIF maps can be **georeferenced** using tools like [Allmaps](https://allmaps.org/), which enables us to align historic imagery with modern geographic space and extract meaningful spatial data from it.

In this workshop, we’ve used a **IIIF manifest URL** , which is a special web link that points to structured information about a digitized image—in this case, John Snow’s 1854 cholera map. This manifest allows us to load the map into georeferencing tools like [Allmaps Editor](https://allmaps.org) and generate an **XYZ tile service** that we can bring into a number of different GIS applications.

That's where this workshop picks up. We’ll use the georeferenced map in [geojson.io](https://geojson.io) as a backdrop to digitize spatial features and create a new dataset.

## Preparation required for working with your own map images

To prepare for the scope of this workshop, we've already uploaded and georeferenced an image of John Snow's Map of the Cholera Outbreak of 1854.

In order to use what you learn in this workhop with your own materials, you will need to have curated (or hosted) a set of maps with IIIF manifest URLs, and georeferenced that IIIF-served map (or maps) using editor.allmaps.org. You can find the steps for both of these tasks, here:

1. **Complete the Archive.org IIIF Image Hosting Tutorial**
   If you want to upload and serve your own map images to archive.org for using IIIF, you can follow the instructions at:
   [https://training.iiif.io/time_machine/day-two/image-servers/iiif-hosting-ia.html](https://training.iiif.io/time_machine/day-two/image-servers/iiif-hosting-ia.html)
   This covers how to upload and publish a IIIF manifest using the Internet Archive.
2. **Complete the Allmaps Georeferencing Tutorial**
   Once you have an image of a map at a IIIF endpoint, you can georeference it, and get an XYZ Tile URL using the following instructions:
   [https://allmaps.org/#getting-started/](https://allmaps.org/#getting-started/)
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
