Here is a comprehensive and structured `README.md` file for your GitHub repository. It incorporates the project title, describes the workflow of your Google Earth Engine (GEE) script, and includes placeholders for the images you provided.

To use this, simply copy the text below into your `README.md` file and ensure the image files (`ee-chart.png`, `occurance.png`, `1998.png`, `2021.png`) are uploaded to the root of your repository.

---

```markdown
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

## 🛠️ Data Sources

* **Dataset:** [JRC Global Surface Water Mapping Layers, v1.4](https://developers.google.com/earth-engine/datasets/catalog/JRC_GSW1_4_GlobalSurfaceWater)
* **Yearly History:** [JRC Global Surface Water Yearly History, v1.4](https://developers.google.com/earth-engine/datasets/catalog/JRC_GSW1_4_YearlyHistory)
* **Scale:** 30 meters
* **Timeframe:** 1984 – 2021 (Dataset coverage limit)

---

## 🚀 How to Run the Code

1.  Sign up for a [Google Earth Engine](https://earthengine.google.com/) account if you don't already have one.
2.  Open the [Google Earth Engine Code Editor](https://code.earthengine.google.com/).
3.  Copy the JavaScript code from this repository and paste it into the code editor.
4.  Click **Run** to execute the script.
5.  Check the **Console** tab to view the calculated yearly area, the generated chart, and specific key-year comparisons.
6.  Go to the **Tasks** tab to run the export tasks, which will save the `.csv` data and map images directly to your connected Google Drive folder (`EarthEngineExports`).

---

## 📂 Exported Outputs

Running the export tasks will generate the following files in your Google Drive:
1.  `lake_urmia_yearly_area.csv` - The time-series data of the lake's area in square kilometers.
2.  `lake_urmia_occurrence_map.tif` - The spatial map of water occurrence probabilities.
3.  `lake_urmia_1998.tif` - The classified water extent map for 1998.
4.  `lake_urmia_2021.tif` - The classified water extent map for 2021.

```
