# UCL MRes Thesis: Spatial Segregation Analysis of São Paulo Metropolitan Area

## About

This repository contains the datasets, analytical code and workflows for an MRes dissertation examining spatial segregation patterns in the São Paulo Metropolitan Area (SPMA) using origin-destination survey data from 2023. The analysis employs hexagonal grid spatial indexing (H3), entropy-based measures, and Local Indicators of Spatial Association (LISA) to investigate socioeconomic and trip motive patterns across temporal and spatial dimensions.

## Study Context

**Study Area**: São Paulo Metropolitan Area (SPMA), Brazil  
**Temporal Scope**: 2023 Origin-Destination Survey  
**Spatial Resolution**: H3 hexagonal grid at resolution 9  
**Study Population**: Adults (18+ years) from the 2023 OD survey

### Socioeconomic Classification

The analysis uses Socioeconomic Groups (SEG) according to the Brazilian Economic Classification Criterion (BCCE) (ABEP, 2024):

- **High (A)**: CRITERIOBR code 1
- **Upper middle (B1/B2)**: CRITERIOBR codes 2-3
- **Lower middle (C1/C2)**: CRITERIOBR codes 4-5
- **Low (D/E)**: CRITERIOBR code 6

## Methodology

### Spatial Representation

The analysis uses the **H3 hierarchical hexagonal grid system at resolution 9**, which provides:

- **Average hexagon area**: ~0.10 km² 
- **Edge length**: ~174 metres
- **Consistent spatial units**: Unlike administrative boundaries (e.g., census tracts), H3 hexagons provide uniform, comparable spatial units ideal for segregation analysis in urban areas
- **Hierarchical structure**: Resolution 9 balances spatial granularity with computational efficiency, allowing detailed intra-urban analysis while maintaining manageable data volumes

### Temporal Resolution

Temporal analyses use **15-minute intervals** (96 periods per day), enabling high-resolution examination of segregation patterns, peak-hour dynamics (intervals grouped into 2-hour windows, and activity space variations throughout the day.

## Repository Structure

```
ucl-mres-thesis/
├── Final Code/
│ ├── Step1_data-processing-and-representation.ipynb
│ ├── Step2_data-analysis_frequencies.ipynb
│ ├── Step3_motive-entropy-calculations.ipynb
│ └── Step4_Residential-strongholds.ipynb
├── outputs/
│ ├── cleandbf.csv
│ ├── od_with_h3.csv
│ ├── OD_ADULTS_Timeindex_Motive_location_tripexpansionfactors.csv
│ ├── econ_entropy.csv
│ ├── motive_entropy.csv
│ ├── H3_res9/
│ │ └── Municipios_2023_h3_res9.shp
│ └── [various visualisation outputs in PNG format]
├── [data directories - see Data section below]
└── README.md
```


## Requirements

- **Python**: 3.11
- **Environment**: Jupyter Notebook

Core dependencies include: `pandas`, `geopandas`, `numpy`, `matplotlib`, `seaborn`, `h3`, `shapely`, `folium`, `simpledbf`, `libpysal`, `esda`

## Data Sources

All data used in this analysis is publicly available and included in the repository. The original datasets can also be accessed from the [São Paulo Metro Origin-Destination Survey portal](https://www.metro.sp.gov.br/pesquisa-od/).

- **OD2023 DATASET-Metro SP/**: Origin-Destination survey data (DBF format).
- **OD2023 Supplementary materials/**: Original and translated report book, manual and lookout table.
- **Municipal boundaries-ShapeFiles/**: SPMA municipal boundary shapefiles
- **SPMA Urbanised Areas-MapBiomas/**: 2022 urbanised area polygons
- **Supplementary Tables-Metro SP/**: additional documents provided by Metro SP.

Refer to the respective data directories in the repository for file locations.

## Workflow

The analysis follows a sequential four-step workflow. **Execute notebooks in order:**

### Step 1: Data Processing and Representation
**Notebook**: `Final Code/Step1_data-processing-and-representation.ipynb`

**Purpose**: Load, clean, and spatially index origin-destination survey data

**Key Operations**:
- Load OD survey DBF files and municipal shapefiles
- Filter invalid/zero-coordinate records
- Generate H3 hexagonal grid at resolution 9 for SPMA
- Spatial join: assign origin and destination hexagons to each trip
- Create 15-minute temporal windows (96 intervals per day)
- Transform data into Person-Time-Trip Index (PTTI) format: wide dataframe where each row represents one person, and each column represents a 15-minute interval with associated location (hexagon), trip motive, and trip expansion factor

**Outputs**:
- `outputs/cleandbf.csv`: Cleaned OD data with H3 hexagons and temporal indices
- `outputs/od_with_h3.csv`: OD data with geometric information
- `outputs/OD_ADULTS_Timeindex_Motive_location_tripexpansionfactors.csv`: Wide-format PTTI dataframe
- `outputs/H3_res9/Municipios_2023_h3_res9.shp`: H3 hexagonal grid shapefile

---

### Step 2: Data Analysis - Frequencies
**Notebook**: `Final Code/Step2_data-analysis_frequencies.ipynb`

**Purpose**: Analyse temporal patterns of trip frequencies and activity timing

**Key Operations**:
- Calculate normalised departure and arrival frequencies by quarter-hour
- Disaggregate temporal patterns by socioeconomic group
- Generate heatmaps of trip motives × time periods
- Smooth and visualise temporal profiles across economic groups

**Outputs**:
- Trip frequency plots (overall and by economic group)
- Normalised departure/arrival visualisations (PNG)
- Heatmaps of trip motives by time and economic group (PNG)

---

### Step 3: Motive Entropy Calculations
**Notebook**: `Final Code/Step3_motive-entropy-calculations.ipynb`

**Purpose**: Compute expansion-weighted entropy measures for trip motives and socioeconomic groups

**Key Operations**:
- Calculate Shannon entropy of trip motives per hexagon (all-day and peak-window)
- Calculate Shannon entropy of economic group presence per hexagon
- Restrict analysis to urbanised areas only (using MapBiomas urbanised polygons)
- Perform Local Moran's I (LISA) spatial autocorrelation analysis for entropy measures
- Identify spatial clusters (High-High, Low-Low, High-Low, Low-High)
- Generate temporal profiles of mean entropy across 15-minute intervals

**Outputs**:
- `outputs/motive_entropy.csv`: Trip motive entropy per hexagon
- `outputs/econ_entropy.csv`: Economic group entropy per hexagon
- `outputs/MOTIVE_Lisa_Cluster_Summary.csv`: LISA cluster counts by time period
- Entropy maps for urbanised areas (PNG)
- LISA cluster maps for peak periods (PNG)
- Temporal entropy profiles (PNG)

---

### Step 4: Residential Strongholds
**Notebook**: `Final Code/Step4_Residential-strongholds.ipynb`

**Purpose**: Identify and characterise socioeconomic residential strongholds

**Key Operations**:
- Calculate all-day normalised density of each economic group per hexagon
- Define "strongholds" as hexagons above 75th percentile density for each group
- Overlay entropy measures (trip motive and economic) onto stronghold hexagons
- Generate comparative maps showing entropy variation within strongholds
- Compute descriptive statistics of entropy within each group's strongholds

**Outputs**:
- Stronghold identification maps (PNG)
- Entropy-in-strongholds choropleth maps by economic group (PNG)
- Summary statistics of entropy distributions within strongholds

---

## Output Files

### Data Outputs (CSV)
- `cleandbf.csv`: Cleaned origin-destination dataset
- `od_with_h3.csv`: OD data with H3 spatial indices
- `OD_ADULTS_Timeindex_Motive_location_tripexpansionfactors.csv`: PTTI wide-format dataframe
- `econ_entropy.csv`: Economic group entropy per hexagon
- `motive_entropy.csv`: Trip motive entropy per hexagon
- `MOTIVE_Lisa_Cluster_Summary.csv`: LISA cluster counts

### Spatial Outputs (Shapefiles)
- `H3_res9/Municipios_2023_h3_res9.shp`: H3 hexagonal grid covering SPMA

### Visualisation Outputs (PNG)
- Temporal frequency plots (departures/arrivals by time and economic group)
- Heatmaps (trip motives × time periods)
- Entropy choropleth maps (motive and economic entropy)
- LISA cluster maps (spatial autocorrelation patterns)
- Residential stronghold maps (density and entropy overlays)

All visualisations are exported at 300 DPI resolution suitable for publication.

## Usage

1. Ensure Python 3.11 and Jupyter Notebook are installed
2. Install required dependencies
3. Clone this repository
4. Execute notebooks sequentially from Step 1 to Step 4
5. Outputs will be generated in the `outputs/` directory

## Notes

- Analysis restricted to **adults (18+ years)** from the survey
- **Urbanised areas only** constraint applied in entropy calculations (Steps 3-4)
- Trip expansion factors (`FE_VIA`) and person expansion factors (`FE_PESS`) are used throughout to ensure representative population-level estimates
- "Transit" periods (where person is travelling between locations) are handled explicitly in PTTI dataframe construction

## References

ABEP (2024). Brazilian Economic Classification Criterion (BCCE).

---

**Author**: [Afsun Mehmani]  
**Institution**: University College London (UCL)  
**Programme**: MRes [Urban Spatial Science]  
**Academic Year**: 2024-2025
