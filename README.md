
# GeoDispatch — Autonomous EV Fleet Dispatch Optimizer

> A constrained multi-vehicle dispatch optimization engine simulating real-world EV fleet routing under battery range limits — built on real geospatial data.

---

## 🚗 Vision

As autonomous EV fleets scale globally, the core challenge shifts from navigation to **dispatch optimization** — how do you route hundreds of vehicles across a city, minimize total trips, reduce energy consumption, and guarantee every delivery stop is covered within a single charge?

GeoDispatch tackles exactly this problem: given a real geospatial network of delivery stops, hard per-vehicle battery range constraints, and geofenced operational zones — find the optimal multi-vehicle dispatch plan that minimizes fleet trips and total distance traveled.

---

## ⚙️ Technical Approach

The problem belongs to the class of **NP-hard Vehicle Routing Problems (VRP)** — exact solutions become computationally infeasible as the number of stops grows. We evaluated multiple approaches:

| Approach | Decision |
|---|---|
| Minimum Spanning Tree (MST) | ❌ Discarded — doesn't guarantee valid traversal paths |
| Brute-force exact solver | ❌ Infeasible at scale (exponential time complexity) |
| Google OR-Tools VRP | ✅ Adopted — efficient heuristics + constraint handling |

### Pipeline

```
1. Data Loading       → Real geospatial dataset (lat/long waypoints, polygon boundaries)
2. TSP Initialization → Unconstrained route via PATH_CHEAPEST_ARC strategy
3. Path Reconstruction → Dijkstra-based decoding via predecessors matrix
4. Geofence Validation → Shapely polygon enforcement on all flight/delivery paths
5. Mission Segmentation → Split routes into range-valid dispatch runs
6. Dynamic Replanning  → Distance matrix updated after each completed run
7. Benchmarking        → 12 solver configurations evaluated for optimal cost-function
8. Visualization       → Route maps plotted via GeoPandas + Matplotlib
```

### Algorithms
- **OR-Tools Guided Local Search** — primary VRP optimization metaheuristic
- **PATH_CHEAPEST_ARC** — first solution strategy for TSP initialization
- **Dijkstra path reconstruction** — decodes indirect routes via predecessors matrix ensuring polygon adherence
- **Disjunction penalties** — handles infeasible nodes gracefully without failing the solver

---

## 📊 Results

Benchmarked across 12 solver time configurations to identify optimal trade-off between solution quality and compute time:

| Solver Time (s) | Total Distance (ft) | Missions |
|---|---|---|
| 10 | 406,605 | 12 |
| 30 | 381,606 | 11 |
| 80 | **376,523** ✅ | **11** |
| 120 | 380,787 | 11 |

**Optimal configuration: 80 seconds solver time**
- **7.4% reduction** in total dispatch distance vs baseline
- **1 fewer mission** vs naive configuration
- **100% delivery stop coverage** across all sequential dispatch runs
- Full geofenced zone compliance across all 11 missions

---

## 🗺️ Dataset

Built on a **real Florida state geospatial dataset** — including:
- Latitude/longitude coordinates for all delivery waypoints
- Precomputed shortest-path distance matrix (N × N)
- Dijkstra predecessor matrix for path reconstruction
- Polygon boundary defining the operational geofenced zone

> ⚠️ Raw data files are not included in this repository due to confidentiality. The pipeline code is fully reproducible with equivalent geospatial datasets.

---

## 🛠️ Tech Stack

| Category | Tools |
|---|---|
| Optimization | Google OR-Tools, VRP Solver |
| Geospatial | GeoPandas, Shapely, Plotly Mapbox |
| Data Processing | NumPy, SciPy |
| Visualization | Matplotlib, GeoPandas |
| Language | Python |

---

## 🚀 How to Run

```bash
# Install dependencies
pip install ortools shapely geopandas matplotlib numpy

# Run the optimizer
jupyter notebook Drp.ipynb
```

> Provide equivalent geospatial dataset files in the working directory:
> `distance_matrix.npy`, `predecessors.npy`, `points_lat_long.npy`, `polygon_lon_lat.wkt`

---

## 📈 Future Work
- Scale to **multi-depot** dispatch (multiple charging stations)
- Integrate **real-time traffic/road network** data via OSMnx
- Extend to **time-window constrained** delivery scheduling
- Benchmark against **reinforcement learning** based dispatch policies

---

*Built with the vision of making autonomous EV fleet dispatch efficient, scalable, and geospatially aware.*

---
