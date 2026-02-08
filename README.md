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

- Al-Rabiaah, A., Al-Tuwaijri, H., & Al-Zalabani, A. (2022). The use of geographic information systems for the optimal location of healthcare facilities: A systematic review. Egyptian Journal of Health Care, 13(1), 1018–1033. https://doi.org/10.21608/ejhc.2022.215570. (Accessed January 20, 2026)
- Asadi, H., Rabiei-Dastjerdi, H., & Tavana, M. (2021). A GIS-based multi-criteria decisionmaking approach for healthcare accessibility assessment. Applied Geomatics, 13, 561–572. https://doi.org/10.1007/s12518-021-00366-2. (Accessed January 19, 2026)
- Berke, E. M., Tanski, S. L., Demidenko, E., Alperstein, G., Zahl, K., & Shen, X. (2008). Proximity of schools to residences and the risk of traffic injuries to walking and bicycling students. American Journal of Preventive Medicine, 34(4), 282–290. https://doi.org/10.1016/j.amepre.2008.01.024. (Accessed January 26, 2026)
- Bertoli, S., & Grembi, V. (2017). The life-saving effect of hospital proximity. Health Economics, 26(5), 644–659. https:/doi.org/10.1002/hec.3571. (Accessed January 19, 2026)
- Boeing, G. (2017). OSMnx: New methods for acquiring, constructing, analyzing, and visualizing complex street networks.Computers, Environment and Urban Systems, 65, 126–139. https://doi.org/10.1016/j.compenvurbsys.2017.05.004. (Accessed January 26, 2026)
- Chen, X., Jia, P., & Xierali, I. M. (2022). Equity of distribution of healthcare resources in China: A systematic review. International Journal for Equity in Health, 21(1), 145. https://doi.org/10.1186/s12939-022-01748-w. (Accessed January 26, 2026)
- Copernicus Global Human Settlement Layer (GHSL) (n.d.). GHS-POP R2023A - GHS population grid multitemporal (1975-2030). European Commission, Joint Research Centre (JRC). (Accessed January 20, 2026)
- Gwalani, A., Boeing, G., & You, L. (2021). A Greedy Heuristic to Solve a Version of the p-Median Problem. In Proceedings of the 29th International Conference on Advances in Geographic Information Systems (SIGSPATIAL ’21), 60–63. https://doi.org/10.1145/ 3486640.3491391. (Accessed January 20, 2026)
- Hagberg, A., Schult, D. A., & Swart, P. J. (2008). Exploring network structure, dynamics, and function using NetworkX.Proceedings of the 7th Python in Science Conference (SciPy2008), 11–15. (Accessed January 26, 2026)
- Janssen, P., Smith, A., & Lee, C. (2021). GIS–AHP applications in urban healthcare accessibility: A review. (Accessed January 20, 2026)
- MapOG (2024). Open street-level medical facilities dataset. https://mapog.org/datasets. (Accessed January 19, 2026)
- Mseke, C. (2024). Emergency service response times in urban areas. (Accessed January 19,2026)
- OpenStreetMap (OSM) (n.d.). Vector datasets: Medical facilities, street network, administrative boundaries. https://www.openstreetmap.org. (Accessed January 27, 2026)
- Pourrezaie-Khaligh, A., Mousavi, S. M., &Tavakkoli-Moghaddam, R. (2022). A systematic review of location-allocation problems in healthcare. Journal of Industrial and Systems Engineering. (Accessed January 26, 2026)
- Rabiei-Dastjerdi, H., Mohammadi, S., Samouei, R., Kazemi, M., Matthews, S. A., McArdle, G., Homayouni, S., Kiani, B., & Sadeghi, R. (2023). Measuring spatial accessibility to healthcare facilities in Isfahan Metropolitan Area, Iran. ISPRS Annals of the Photogrammetry, Remote Sensing and Spatial Information Sciences, X-4/W1-2022, 623–630. https://doi.org/10.5194/isprs-annals-X-4-W1-2022-623-2023. (Accessed January 19, 2026)
- Saaty, T. L. (1977). A scaling method for priorities in hierarchical structures. Journal of Mathematical Psychology, 15(3), 234–281. https://doi.org/10.1016/0022-2496(77)90033-5. (Accessed January 26, 2026)
- Saaty, T. L. (1990). How to make a decision: The analytic hierarchy process. European Journal of Operational Research, 48(1), 9–26. https://doi.org/10.1016/0377-2217(90)90057-I. (Accessed January 26, 2026)
- Salami, A., Rabiei-Dastjerdi, H., & Tavana, M. (2023). Evaluating spatial accessibility to healthcare facilities using GIS-based Multi-Criteria Decision-Making (MCDM). Journal of Public Health. https://doi.org/10.1007/s10389-023-01832-6. (Accessed January 26, 2026)
- Schuurman, N., Bell, N. J., & Dunn, J. R. (2010). Upper-scale deprivation indices, population health and geography: an evaluation of the spatial effectiveness of indices at multiple scales. International Journal of Health Geographics, 9(1), 30. https://doi.org/10.1186/1476- 072X-9-30. (Accessed January 26, 2026)
- Statistics Canada (2021). Access to primary care providers and travel thresholds for GP access. https://www.statcan.gcca.(Accessed January 19, 2026)
- Tavana, M., Asadi, H., & Rabiei-Dastjerdi, H. (2023). A healthcare accessibility planning support system using GIS and multi-criteria decision-making. (Accessed January 19, 2026)
- Todd, A., Copeland, A., Learoyd, A., Husband, A., Kasim, A., & Bambra, C. (2015). The accessibility of pharmacies in northern England: a cross-sectional study. BMJ Open, 5(3),e007032. https://doi.org/10.1136/bmjopen-2014-007032. (Accessed January 19, 2026)
- Veldwijk, J., Lambooij, M. S., de Bekker-Grob, E. W., Smit, H. A., & de Wit, G. A. (2015). The Analytic Hierarchy Process in Health Care Decisions: A Systematic Review and Guidelines for Reporting. PharmacoEconomics, 33(4), 361–371. https://doi.org/10.1007/s40273-014-0243-7. (Accessed January 19, 2026)