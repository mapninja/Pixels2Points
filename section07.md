## 7. Making a Web Map (with John Snow’s Map Background & Layer Controls)

In this section, you'll bring everything together to build a functional, interactive web map using:

* **Leaflet.js** for map rendering
* **GeoJSON layers** for digitized features (water pumps and cholera deaths)
* **A custom XYZ tile layer** for the georeferenced John Snow map
* **Turf.js** for geospatial analysis
* **Layer controls** so users can toggle visibility of different data layers

---

## Base Leaflet Map with Pumps and Deaths

Here’s your updated boilerplate HTML to include the **Snow map tile layer** and Leaflet **layer groups** for full control.

### Copy & Paste: Full HTML Map Example

```html
<!DOCTYPE html>
<html>
<head>
  <title>John Snow Cholera Web Map</title>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <!-- Leaflet CSS & JS -->
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

  <!-- Turf.js for spatial analysis -->
  <script src="https://unpkg.com/@turf/turf@6.5.0/turf.min.js"></script>

  <style>
    body { margin: 0; }
    #map { height: 100vh; }
  </style>
</head>
<body>
  <div id="map"></div>

  <script>
    // Initialize the map
    const map = L.map('map').setView([51.513, -0.137], 17);

    // Base tile layer (OpenStreetMap)
    const osm = L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '© OpenStreetMap contributors'
    });

    // Custom Snow map tile layer
    const snowMapTiles = L.tileLayer('https://allmaps.xyz/images/45bbc7ea6e45e5c9/{z}/{x}/{y}.png', {
      attribution: 'John Snow map via Allmaps',
      opacity: 0.8
    });

    // Add one basemap by default
    osm.addTo(map);

    // Create empty layer groups
    const pumpLayer = L.layerGroup();
    const deathLayer = L.layerGroup();
    const voronoiLayer = L.layerGroup();
    const meanLayer = L.layerGroup();

    // Load pumps
    fetch('data/snow_map_pumps.geojson')
      .then(res => res.json())
      .then(pumpData => {
        const pumpGeo = L.geoJSON(pumpData, {
          pointToLayer: (feature, latlng) => {
            return L.circleMarker(latlng, {
              radius: 6,
              color: '#000',
              fillColor: '#fff',
              fillOpacity: 1,
              weight: 2
            }).bindPopup(`Pump: ${feature.properties.label}`);
          }
        }).addTo(pumpLayer);

        // Create Voronoi diagram
        const voronoi = turf.voronoi(pumpData, {
          bbox: [-0.144, 51.509, -0.13, 51.517]
        });

        L.geoJSON(voronoi, {
          style: {
            color: '#0077cc',
            weight: 1,
            fillOpacity: 0.1
          }
        }).addTo(voronoiLayer);
      });

    // Load deaths
    fetch('data/snow_map_deaths.geojson')
      .then(res => res.json())
      .then(deathData => {
        const deathsGeo = L.geoJSON(deathData, {
          pointToLayer: (feature, latlng) => {
            const deaths = feature.properties.deaths || 1;
            return L.circleMarker(latlng, {
              radius: Math.sqrt(deaths) * 2,
              fillColor: 'red',
              color: 'darkred',
              weight: 1,
              fillOpacity: 0.7
            }).bindPopup(`Deaths: ${deaths}`);
          }
        }).addTo(deathLayer);

        // Weighted spatial mean
        const weighted = deathData.features.map(f => ({
          ...f,
          properties: {
            ...f.properties,
            weight: f.properties.deaths || 1
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
        }).addTo(meanLayer);
      });

    // Add all overlay layers to the map
    pumpLayer.addTo(map);
    deathLayer.addTo(map);
    voronoiLayer.addTo(map);
    meanLayer.addTo(map);

    // Layer controls
    const baseMaps = {
      "OpenStreetMap": osm,
      "Snow Map (Historical)": snowMapTiles
    };

    const overlayMaps = {
      "Water Pumps": pumpLayer,
      "Cholera Deaths": deathLayer,
      "Voronoi Diagram": voronoiLayer,
      "Weighted Mean Center": meanLayer
    };

    L.control.layers(baseMaps, overlayMaps).addTo(map);
  </script>
</body>
</html>
```

---

## Summary of Layers


| Layer Name            | Description                                              |
| ----------------------- | ---------------------------------------------------------- |
| OpenStreetMap         | Modern basemap                                           |
| Snow Map (Historical) | 1854 map as XYZ tile layer (from Allmaps)                |
| Water Pumps           | Your digitized GeoJSON pump data                         |
| Cholera Deaths        | Proportional symbols sized by number of deaths           |
| Voronoi Diagram       | Area of influence polygons for each water pump (Turf.js) |
| Weighted Mean Center  | Single point showing spatial mean of deaths (Turf.js)    |
