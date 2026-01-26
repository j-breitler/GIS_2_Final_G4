# Medical Care Supply in Graz: Distribution & Quality Analysis

A GIS-based analysis of healthcare accessibility in Graz, Austria. This project calculates accessibility scores for every location in the city based on network distances to medical facilities, identifies underserved areas, and determines the optimal location and type for a new facility to maximize overall accessibility.

## Research Questions

1. **Distribution Mapping:** How are medical care facilities distributed across Graz?
2. **Quality Assessment:** What is the accessibility quality in each district, and which areas are underserved?
3. **Optimization:** Where should a new facility be built, and what impact would it have on accessibility?

## Methodology Overview

### Accessibility Score Calculation

The accessibility score for each location is calculated using:

- **Network Distance:** Walking distances via multi-source Dijkstra algorithm (~39,000 street network nodes)
- **AHP Weighting:** Relative importance of facility types using Analytic Hierarchy Process
- **Quadratic Decay:** Distance-to-score transformation: `score = max(0, 1 - (dist/dmax)²) × weight`

| Facility Type | AHP Weight | Max Distance | Rationale |
|---------------|------------|--------------|-----------|
| Hospital | 0.545 | 3000m | Emergency/acute care (highest priority) |
| Clinic | 0.193 | 1200m | Intermediate care |
| Doctor | 0.193 | 1000m | Primary care |
| Pharmacy | 0.069 | 800m | Routine medication access |

### Population Weighting

Accessibility scores are weighted by population density to identify where improvements matter most:
```
weighted_score = 1 - (population/max_population) × (1 - accessibility_score)
```

## Project Workflow

### Step 1: Data Acquisition
- **Boundaries:** City and district polygons from OpenStreetMap (17 districts)
- **Medical Facilities:** Hospitals, clinics, doctors, pharmacies from OSM (~1090 points)
- **Street Network:** Walking network via OSMnx
- **Population:** GHSL raster (100m resolution)

### Step 2: Distribution Visualization
- Kernel Density Estimation (KDE) maps
- Hexagonal binning
- Choropleth maps by district

### Step 3: Accessibility Metric Calculation
- Extract network nodes as analysis points
- Snap facilities to nearest network nodes
- Calculate network distances using multi-source Dijkstra
- Apply quadratic decay scoring with AHP weights
- Normalize scores to [0, 1] range

### Step 4: Population-Weighted Analysis
- Reproject population raster to metric CRS (EPSG:31256)
- Interpolate node scores to raster grid
- Calculate population-weighted accessibility
- Aggregate statistics by district

### Step 5: Optimal Facility Placement
- Generate candidate locations from underserved areas (bottom 30th percentile)
- Test each candidate with each facility type
- Calculate improvement in overall population-weighted score
- Select best (location, type) combination

## Data Sources

| Data | Source | Format |
|------|--------|--------|
| City boundary | OpenStreetMap via OSMnx | Polygon |
| Districts | OpenStreetMap | Polygons |
| Medical facilities | OpenStreetMap | Points |
| Street network | OpenStreetMap via OSMnx | Graph |
| Population | Global Human Settlement Layer (GHSL) | Raster (100m) |

## Project Structure

```
GIS_2_Final_G4/
├── G4_Final_Project.ipynb     # Main analysis notebook
├── README.md                  # This file
├── Project_explanation.md     # Detailed methodology documentation
├── data/
│   ├── raw/                   # Source data (population raster)
│   ├── processed/             # Processed vector data (parquet, geojson)
│   ├── raster/                # Processed rasters
│   └── output/
│       ├── maps/              # Static map visualizations
│       ├── charts/            # Statistical charts
│       └── interactive/       # HTML maps (Folium, KeplerGL)
```

## Requirements

```
geopandas
osmnx
rasterio
networkx
scipy
numpy
pandas
matplotlib
seaborn
folium
keplergl
rasterstats
shapely
```

Install with:
```bash
pip install geopandas osmnx rasterio networkx scipy numpy pandas matplotlib seaborn folium keplergl rasterstats shapely
```

## Coordinate Reference Systems

| CRS | EPSG Code | Usage |
|-----|-----------|-------|
| WGS84 | 4326 | Web maps, GPS coordinates |
| MGI Austria Lambert | 31256 | All metric calculations (distances in meters) |

## Outputs

- **Accessibility maps:** Node-level and district-level accessibility scores
- **Population-weighted rasters:** Accessibility weighted by population distribution
- **Optimization results:** Recommended facility type and location with expected improvement
- **Interactive maps:** KeplerGL and Folium visualizations

## Limitations

- Analysis assumes walking access (not driving/public transit)
- All facilities of the same type are treated equally (no quality weighting)
- OSM walking network may be incomplete in some areas
- Optimization finds best single facility, not optimal set of multiple facilities

## References

- Chen et al. (2022) - Healthcare accessibility weighting
- Berke et al. (2008) - Primary care accessibility
- Todd et al. (2015) - Distance thresholds for healthcare
- GHSL - Global Human Settlement Layer population data
- OSMnx - OpenStreetMap network analysis library