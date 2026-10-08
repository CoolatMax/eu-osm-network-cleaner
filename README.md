# OpenStreetMap Road Network Processing Pipeline (QGIS & ETRS89)

![QGIS](https://img.shields.io/badge/QGIS-3.34_LTR-588240?logo=qgis&logoColor=white)
![Spatial Standard](https://img.shields.io/badge/CRS-EPSG%3A3035%20(ETRS89%20LAEA)-003399)
![Format](https://img.shields.io/badge/Data_Format-OGC_GeoPackage-green)

## Project Overview
This repository contains a standardized GIS data pipeline for extracting, cleaning, reprojecting, and structuring OpenStreetMap (OSM) vector road networks clipped to European Union Local Administrative Units (LAU).

Designed as an entry-level portfolio demonstration following official QGIS Training Manual specifications for spatial data cleanup, metadata stripping, and spatial index optimization.

## Objectives
1. **Extract OSM Infrastructure Data:** Query highway networks via Overpass API / QuickOSM.
2. **Boundary Alignment:** Clip vector features against official Eurostat GISCO LAU municipality boundaries.
3. **CRS Standardization:** Reproject raw `EPSG:4326` (WGS 84) data into European metric projection `EPSG:3035` (ETRS89-extended / LAEA).
4. **Attribute Table Normalization:** Strip redundant OSM tags (`z_order`, `other_tags`, `osm_id`), standardizing target schema for downstream network analysis.
5. **Geospatial Storage:** Store layers inside a single multi-layer OGC GeoPackage (`.gpkg`) with spatial indexing enabled.

---

## Data Sources

| Dataset | Source | Provider | Projection |
| :--- | :--- | :--- | :--- |
| **LAU 2024 Boundaries** | Eurostat GISCO | European Commission | EPSG:3035 |
| **Road Network (`highway=*`)** | OpenStreetMap | OSM Contributors via QuickOSM | EPSG:4326 (Reprojected to EPSG:3035) |

---

## Workflow Implementation

### Step 1: Boundary & Road Extraction
* **QGIS Documentation Reference:** *Manual Sections 2.1 - 2.3, 3.1, 9.2.2*
* Loaded the target municipality boundary from the GISCO LAU vector dataset.
* Executed a QuickOSM query for `key=highway` bounded by the administrative vector canvas extent.

### Step 2: Coordinate Reference System (CRS) Fix
* Raw OSM exports use geographic coordinates (`EPSG:4326` WGS84).
* Reprojected layers to **`EPSG:3035` (ETRS89-extended / LAEA)** to ensure metric distance accuracy for European analysis.

### Step 3: Vector Schema Cleaning & Geometry Validation
* Removed unused OSM metadata attributes to reduce data overhead:
  * Dropped null columns and raw XML tag blobs.
  * Retained core functional keys: `highway`, `name`, `maxspeed`, `surface`, `one_way`.
* Ran `Fix Geometries` tool to remove duplicate nodes and invalid self-intersections.

### Step 4: GeoPackage Compilation
* Exported clean vectors into a structured GeoPackage (`brussels_infrastructure.gpkg`).
* Saved the complete QGIS environment as a portable `.qgz` project using relative paths.

---

## Repository Structure

```text
├── README.md
├── docs/
│   └── workflow_guide.md
│   └── Screenshots
│       └── random.png
├── data/
│   ├── raw/                  # Excluded from version control via .gitignore
│   └── processed/
│       └── example.gpkg
└── qgis/
    └── example.qgz
