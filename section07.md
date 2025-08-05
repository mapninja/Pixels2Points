## 7. Making a Web Map (Draft UNDER CONSTRUCTION, probably broken)

This section shows you how to build an interactive web map using:

* **Leaflet.js** for mapping
* **Turf.js** for spatial analysis
* Custom and open basemaps
* GeoJSON data you created earlier

We’ll start simple and progressively add new capabilities.

---

### Part 1: Basic Map with Pumps and Deaths

Start with a Leaflet map showing your digitized **water pumps** and **cholera deaths** as proportional symbols.

```html
<!-- Part 1: Basic Leaflet Map with Pumps and Deaths -->
<!DOCTYPE html>
<html>
<head>
  <title>Snow Cholera Map</title>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <style> body { margin: 0; } #map { height: 100vh; } </style>
</head>
<body>
  <div id="map"></div>
  <script>
    const map = L.map('map').setView([51.513, -0.137], 17);
    L.tileLayer('https://a.basemaps.cartocdn.com/light_all/{z}/{x}/{y}.png', {
      attribution: '© CartoDB, OSM contributors'
    }).addTo(map);

    // Water pumps
    fetch('snow_map_pumps.geojson')
      .then(res => res.json())
      .then(data => {
        L.geoJSON(data, {
          pointToLayer: (feature, latlng) => L.circleMarker(latlng, {
            radius: 10, color: 'white', fillColor: 'blue',
            fillOpacity: 1, weight: 2
          }).bindPopup(`Pump: ${feature.properties.label}`)
        }).addTo(map);
      });

    // Cholera deaths
    fetch('snow_map_deaths.geojson')
      .then(res => res.json())
      .then(data => {
        L.geoJSON(data, {
          pointToLayer: (feature, latlng) => {
            const deaths = feature.properties.Num_Cases || 1;
            return L.circleMarker(latlng, {
              radius: Math.sqrt(deaths) * 4,
              fillColor: 'red', color: 'darkred',
              weight: 1, fillOpacity: 0.7
            }).bindPopup(`Deaths: ${deaths}`);
          }
        }).addTo(map);
      });
  </script>
</body>
</html>
```

---

### Part 2: Adding the Snow Map Raster Tile Layer

Replace or add to the basemap using the historical **John Snow map** served via **Allmaps.xyz**.

Add the following **after** initializing the map:

```javascript
// Add historical map as a tile layer
const snowMapTiles = L.tileLayer('https://allmaps.xyz/images/45bbc7ea6e45e5c9/{z}/{x}/{y}.png', {
  attribution: 'John Snow map via Allmaps',
  opacity: 0.8
});
snowMapTiles.addTo(map); // optional to start with historical view
```

You can toggle between this and the Carto base layer later using controls (see Part 4).

---

### Part 3: Enhancing with Turf.js (Voronoi + Weighted Mean)

Add [Turf.js](https://turfjs.org) to perform spatial analysis: build **Voronoi regions** around pumps and compute a **weighted spatial mean** of cholera deaths.

Add this script in the `<head>` section:

```html
<script src="https://unpkg.com/@turf/turf@6.5.0/turf.min.js"></script>
```

Then insert this logic inside the `<script>` after loading pump and death data:

```javascript
// Pump Voronoi polygons
fetch('snow_map_pumps.geojson')
  .then(res => res.json())
  .then(pumpData => {
    const voronoi = turf.voronoi(pumpData, {
      bbox: [-0.144, 51.509, -0.13, 51.517]
    });

    L.geoJSON(voronoi, {
      style: {
        color: 'red',
        weight: 2,
        fillOpacity: 0.01
      }
    }).addTo(map);
  });

// Weighted spatial mean of deaths
fetch('snow_map_deaths.geojson')
  .then(res => res.json())
  .then(deathData => {
    const weighted = deathData.features.map(f => ({
      ...f,
      properties: {
        ...f.properties,
        weight: f.properties.Num_Cases || 1
      }
    }));

    const center = turf.centerMean(turf.featureCollection(weighted), {
      weight: "weight"
    });

    L.geoJSON(center, {
      pointToLayer: (feature, latlng) => {
        return L.circleMarker(latlng, {
          radius: 10,
          color: 'black',
          fillColor: 'yellow',
          fillOpacity: 0.9
        }).bindPopup("Weighted spatial mean of deaths");
      }
    }).addTo(map);
  });
```

---

### Part 4: Add Layer Groups and Toggle Controls

To allow toggling layers on/off, define **LayerGroups** and use `L.control.layers()`.

#### Step 1: Create empty LayerGroups

Before fetching data, define these groups:

```javascript
const pumpLayer = L.layerGroup().addTo(map);
const deathLayer = L.layerGroup().addTo(map);
const voronoiLayer = L.layerGroup().addTo(map);
const meanLayer = L.layerGroup().addTo(map);
```

#### Step 2: Add features to those groups

When creating layers, replace `.addTo(map)` with `.addTo(pumpLayer)`, `.addTo(deathLayer)`, etc.

#### Step 3: Add layer controls at the end

```javascript
const baseMaps = {
  "CARTO Positron": osm,
  "Snow Map (Historical)": snowMapTiles
};

const overlayMaps = {
  "Water Pumps": pumpLayer,
  "Cholera Deaths": deathLayer,
  "Voronoi Diagram": voronoiLayer,
  "Weighted Mean Center": meanLayer
};

L.control.layers(baseMaps, overlayMaps).addTo(map);
```

---
