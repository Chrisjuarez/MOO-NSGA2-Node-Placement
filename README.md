## 🔥 NSGA-II Sensor Placement for Wildfire Monitoring

This project implements a multi-objective genetic algorithm (NSGA-II) to optimize the placement of wildfire detection sensors. It uses real elevation data from OpenTopography and walkable routes from OpenStreetMap to identify ideal sensor locations based on coverage, line-of-sight (LOS), elevation, spatial spread, and solar potential.

---

## Features

- **DEM Integration**: Automatically downloads and parses DEM (SRTMGL1) raster data using OpenTopography API
- **Smart Sampling**: Uses walkable OSM nodes with elevation filtering (e.g., 85th percentile and above)
- **Polygon-Aware NSGA-II**: Sensors are initialized in convex polygons to improve boundary coverage
- **LOS Scoring**: Computes LOS score using elevation profile and Fresnel zone clearance
- **NSGA-II Optimization**: Real-coded evolutionary algorithm with BLX-α crossover and polynomial mutation
- **Folium Visualization**: Displays sensors, gateway, coverage, and polygon hull over satellite imagery

---

## Requirements

Install dependencies via Anaconda (recommended):

```bash
conda create -n wildfire-env python=3.12 -y
conda activate wildfire-env
pip install numpy folium matplotlib geopy rasterio shapely osmnx requests
```

---

## How to Run

1. Open `nsga_altadena.ipynb` in **Visual Studio Code**
2. At the top of the notebook interface, click the **kernel dropdown**
3. Select one of the following:

   - `wildfire-env (Python 3.12.11)` ← recommended
   - `base (Python 3.12.2)` ← works if all packages are installed

4. Click **“Run All”** or run each cell top to bottom

---

## ⚙️ Code Overview with Comments

| Section | Purpose |
|--------|---------|
| **DEM Parameters** | Sets constants for sensor height, LOS samples, and buffer zones |
| **Geolocation Setup** | Uses `geopy` to center on "Altadena, California" and derive bounding box |
| **DEM Download** | Calls OpenTopography API and loads the DEM raster into memory |
| **Walkable Node Graph** | Uses `osmnx` to extract walkable paths and assigns elevation using DEM |
| **Elevation Filtering** | Filters nodes by 85th percentile elevation for high-ground sensor placement |
| **Polygonal Chromosome Init** | Initializes NSGA-II population using convex hull polygon shape |
| **LOS Function** | Computes line-of-sight score between sensors and gateway using elevation |
| **Evaluate Function** | Combines cost, LOS, coverage, overlap penalty, and gateway centrality |
| **NSGA-II Components** | Includes domination logic, crowding distance, BLX-α crossover, mutation |
| **Main NSGA-II Loop** | Evolves population for 25 generations, logs best cost/coverage/LOS |
| **Folium Map Output** | Renders sensors, gateway, coverage zones, polygon, and lines on satellite map |

---

## Optimization Objectives

| Objective              | Description                                                      |
|------------------------|------------------------------------------------------------------|
| Cost                | Base cost + sensor distances + overlap penalty + uncovered areas |
| LOS Quality         | Based on terrain obstruction using Fresnel clearance             |
| Polygon Area        | Encourages wide sensor distribution by maximizing convex hull    |
| Coverage            | Based on a grid of points within the bounding domain             |
| Gateway Centrality  | Penalizes gateways placed far from average sensor center         |

---

## File Structure

```bash
├── NSGA2
   └── Notebook
      └── nsga_altadena.ipynb     # GA for Altadena map
      └── nsga_crestline.ipynb    #GA for Crestline map
      └── nsga2build.ipynb          #Construction of NSGA-2 with Booth, Matyas, and Rastrigin Functions and Graphs
          
   ├── README.md                   # Project documentation (you’re reading it)
    
```

---

## ⚠️ Kernel Tips

If VS Code doesn't lint or highlight errors under `base`, but does under `wildfire-env`, that's expected.

To check the active environment inside the notebook, run:

```python
import sys
print(sys.executable)
```

If `wildfire-env` doesn’t show up in your dropdown:
```bash
conda activate wildfire-env
python -m ipykernel install --user --name wildfire-env --display-name "wildfire-env"
```

Then restart VS Code.

---

## Parameters You Can Tweak

| Variable | Description | Default |
|----------|-------------|---------|
| `NUM_SENSORS` | Number of sensors (excluding gateway) | 7 |
| `SENSOR_COVERAGE_RADIUS_M` | Sensor detection radius in meters | 400 |
| `ELEV_PERCENTILE` | Percentile to define "high elevation" | 85 |
| `WEIGHT_COVERAGE` | Coverage objective weight | 4000.0 |
| `WEIGHT_LOS` | LOS objective weight | 2.0 |
| `WEIGHT_POLYGON_AREA` | Spread/area objective weight | 100.0 |
| `WEIGHT_GATEWAY_CENTRALITY` | Penalizes far gateway | 550.0 |
| `MAX_GEN` | Number of generations | 25 |
| `POP_SIZE` | Population size | 580 |

---

## ✅ Expected Output

Once run, the notebook will print generation logs like:

```
GEN 24/25: Best Cost=7140.244, Best Coverage=288.889, Best LOS=0.353
```

And show a map with:
- 🔴 Red star: Gateway node
- 🟢 Green circles: Sensor locations with coverage radius
- 🔷 Blue polygon: Convex hull of all nodes
- 🟠 Orange lines: Sensor-to-gateway links
- 🔲 Blue rectangle: Bounding box of domain

---

## Future Improvements

- [ ] Let client draw bounding circle/polygon from frontend
- [ ] Save solution to GeoJSON or KML
- [ ] Multiple central nodes
- [ ] Budget cost 
---

## Author

**Christian Juarez**  
California State University, Fullerton 
