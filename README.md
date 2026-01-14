# Medical Care Supply in Graz: Distribution & Quality Analysis

This project analyzes the spatial distribution and accessibility of medical care facilities in Graz, Austria, to identify disadvantaged areas and optimize the placement of future facilities. [cite: 2, 4, 6]

## Research Questions
* **Distribution Mapping:** How is the distribution of places for medical care in Graz? [cite: 3]
* **Quality Assessment:** How is the distribution quality in each district or neighbourhood, and which are disadvantaged? [cite: 4, 5]
* **Optimization:** Where should new facilities be built and which impact would that have on the distribution? [cite: 6]

## Project Workflow

### 1. Data Acquisition and Cleaning
* **Facility Data:** OSM data for hospitals, rescue stations, pharmacies, and GPs. [cite: 7]
* **Infrastructure:** OSM street networks and administrative data (city and districts). [cite: 8]
* **Demographics:** GHSL population raster data and OSM residential areas. [cite: 8, 9]
* **Optimization:** Evaluation of GeoParquet for data storage (Extension B). [cite: 7]

### 2. Distribution Calculation & Visualization
* Calculate the density of each category using KDE, hexagons, or choropleth maps by district. [cite: 12]
* Visualize the current distribution across the city. [cite: 13]

### 3. Distribution Quality Metrics
* Evaluate acceptable travel times or distances to specific facility categories. [cite: 15]
* Apply weights to different categories for metric calculation. [cite: 16]
* Calculate metrics for every node in the Graz network. [cite: 17]

### 4. Spatial Analysis
* Interpolate node metrics to raster or vector formats. [cite: 19]
* Calculate and visualize areal statistics for residential areas within each district. [cite: 20, 21]

### 5. Facility Optimization (Scenario Analysis)
* Identify optimal positions and types for new facilities to maximize the overall score (Extension C). [cite: 22]
* Visualize and compare changes for the districts. [cite: 24]

## Team & Task Distribution
* **Person 1:** Tasks 1, 2, 6 (Methods/Results — Density, Parquet, and Data). [cite: 28]
* **Person 2:** Tasks 3, 6 (Introduction, Research Questions, and Metric Methods). [cite: 29]
* **Person 3:** Tasks 4, 6 (Methods/Results — Calculation of Distribution). [cite: 30]
* **Person 4:** Tasks 5, 6 (Methods/Results — Optimization, Discussion, and Conclusion). [cite: 31]

## Report Structure
The final project report covers the following: Introduction & Research Question, Data & Methods, Results, Discussion, Conclusion & Outlook, and References. [cite: 26]