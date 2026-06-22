# Monitoring Lake Urmia Surface Water Changes (1985–2025) using Google Earth Engine

This project utilizes Google Earth Engine (GEE) to analyze and visualize the dramatic changes in the surface water area of Lake Urmia over the past few decades. By leveraging the **JRC Global Surface Water Dataset**, the script calculates yearly surface area, maps water occurrence, and compares historical extents to highlight the ecological changes in the region.

## 🌍 Project Overview

Lake Urmia, located in northwestern Iran, was once one of the largest hypersaline lakes in the world. However, due to climate change, prolonged droughts, and water mismanagement, it has experienced severe shrinkage. This repository provides a GEE JavaScript workflow to quantify and visualize this decline.

### Key Features
* **Water Occurrence Mapping:** Visualizes the long-term probability of finding water in the Lake Urmia basin.
* **Time-Series Analysis:** Calculates the total surface water area (in km²) for each year and generates an interactive line chart.
* **Temporal Comparisons:** Visually compares the lake's extent during a healthy year (1998) versus a depleted year (2021).
* **Automated Data Export:** Exports the time-series area data as a `.csv` and the generated maps as images directly to Google Drive.

---

## 📊 Visualizations

### 1. Surface Water Area Trend
The chart below illustrates the historical trajectory of Lake Urmia's surface area, showing drastic drops and extreme fluctuations over the years.

![Lake Urmia Surface Water Area Chart](ee-chart.png)

### 2. Historical Water Occurrence
This map highlights the frequency of water presence. Deep blue areas represent permanent water bodies, while lighter/reddish areas indicate seasonal or rare water presence.

![Lake Urmia Occurrence Map](occurance.png)

### 3. Comparing 1998 vs. 2021
A stark visual comparison showing the lake's extent before the most severe shrinkage (1998) and its depleted state in recent years (2021).

| 1998 Extent | 2021 Extent |
| :---: | :---: |
| ![Lake Urmia 1998](1998.png) | ![Lake Urmia 2021](2021.png) |
| *Healthy water levels with a broad surface area.* | *Severe shrinkage, leaving behind vast seasonal and dry areas.* |

---

## 💻 Google Earth Engine Script

Below is the complete JavaScript code used for this analysis. You can run this directly in the [Google Earth Engine Code Editor](https://code.earthengine.google.com/).

```javascript
var dataset = ee.Image('JRC/GSW1_4/GlobalSurfaceWater');

var visualization = {
  bands: ['occurrence'],
  min: 0.0,
  max: 100.0,
  palette: ['ffffff', 'ffbbbb', '0000ff']
};

Map.setCenter(59.414, 45.182, 6);

Map.addLayer(dataset, visualization, 'Occurrence');
var geometry = ee.Geometry.Rectangle([44.8, 37.0, 45.7, 38.3]);
var gsw = ee.Image("JRC/GSW1_4/GlobalSurfaceWater");
var occurrence = gsw.select("occurrence").clip(geometry);
var vis = {
  min: 0,
  max: 100,
  palette: ['ffffff', 'ffbbbb', '0000ff']
};
Map.centerObject(geometry, 8);
Map.addLayer(occurrence, vis, "Occurrence");
var water = occurrence.gt(50);
Map.addLayer(water, {palette: ['white', 'blue']}, "Water Mask");
var areaImage = water.multiply(ee.Image.pixelArea());
var stats = areaImage.reduceRegion({
  reducer: ee.Reducer.sum(),
  geometry: geometry,
  scale: 30,
  maxPixels: 1e13
});
print("Lake Area (m²):", stats);

// Step 6: Yearly area calculation
var yearlyCollection = ee.ImageCollection("JRC/GSW1_4/YearlyHistory");

var calculateArea = function(image) {
  var water = image.select("waterClass").eq(2).or(image.select("waterClass").eq(3));
  var areaImage = water.multiply(ee.Image.pixelArea());
  var stats = areaImage.reduceRegion({
    reducer: ee.Reducer.sum(),
    geometry: geometry,
    scale: 30,
    maxPixels: 1e13
  });
  var year = image.get("year");
  return ee.Feature(null, {
    "year": year,
    "area_m2": stats.get("waterClass"),
    "area_km2": ee.Number(stats.get("waterClass")).divide(1e6)
  });
};

var clippedCollection = yearlyCollection.map(function(img) {
  return img.clip(geometry);
});

var areaFeatures = ee.FeatureCollection(clippedCollection.map(calculateArea));
print("Yearly Lake Area Table:", areaFeatures);

// Step 7: Chart
var chart = ui.Chart.feature.byFeature(areaFeatures, "year", "area_km2")
  .setChartType("LineChart")
  .setOptions({
    title: "Lake Urmia Surface Water Area (1985–2025)",
    hAxis: {title: "Year"},
    vAxis: {title: "Area (km²)"},
    lineWidth: 2,
    pointSize: 4,
    colors: ["0000ff"]
  });
print(chart);

// Step 8: Key-year comparison
var keyYears = [1990, 2000, 2010, 2020, 2021];
var keyYearFeatures = areaFeatures.filter(
  ee.Filter.inList("year", keyYears)
);
print("Key Year Comparison:", keyYearFeatures);

Export.table.toDrive({
  collection: areaFeatures,
  description: "LakeUrmia_YearlyArea_1984_2021",
  folder: "EarthEngineExports",
  fileNamePrefix: "lake_urmia_yearly_area",
  fileFormat: "CSV"
});

Export.image.toDrive({
  image: occurrence.visualize(vis),
  description: "LakeUrmia_OccurrenceMap",
  folder: "EarthEngineExports",
  fileNamePrefix: "lake_urmia_occurrence_map",
  region: geometry,
  scale: 30,
  maxPixels: 1e13
});

var img1998 = ee.Image("JRC/GSW1_4/YearlyHistory/1998").select("waterClass").clip(geometry);
var img2021 = ee.Image("JRC/GSW1_4/YearlyHistory/2021").select("waterClass").clip(geometry);

var visClass = {
  min: 0, max: 3,
  palette: ['000000', 'd2b48c', '99d9ea', '0000ff']
  // 0=no data(black), 1=not water(tan), 2=seasonal
