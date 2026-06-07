# Flood Exposure Screening

**Project file:** `Flood_Exposure_Screening.qgz`
**Coordinate reference system:** EPSG:32628 (WGS 84 / UTM Zone 28N)
**Study area:** Sierra Leone
**Date completed:** May 2026

---

## Purpose

This project identifies education facilities in Sierra Leone that are located within flood-prone zones by evaluating their proximity to the national waterway network. Schools within 200 m of any waterway are flagged as flood-exposed, producing a decision-ready dataset that supports school resilience planning, emergency response pre-positioning, and targeted infrastructure investment to protect learning continuity during flood events.

---

## Input layers

| Layer | File | Geometry | Features | CRS |
|---|---|---|---|---|
| Education facilities | `edu_facilities` (source dataset) | Point | 475 | EPSG:32628 |
| Waterway network | `water_lines` (source dataset) | Line | - | EPSG:32628 |

All layers were confirmed in EPSG:32628 before processing.

---

## Processing workflow

### Step 1 - Waterway buffer (200 m flood proximity zone)

A 200 m fixed-distance buffer was generated around all waterway features using **Processing Toolbox > Native > Buffer**, with dissolve enabled to produce a single unified flood proximity polygon. The 200 m threshold represents a practical flood risk proximity standard, accounting for waterway overflow patterns and surface runoff reach. The output was saved as `waterway_buffer_200m.gpkg`.

### Step 2 - Flood risk tagging (Join Attributes by Location)

Each education facility was evaluated for whether it falls within the 200 m waterway buffer using **Processing Toolbox > Native > Join Attributes by Location**. A new text field `flood_risk` was populated using the field calculator:

| Value | Condition |
|---|---|
| `HIGH` | School geometry intersects or falls within `waterway_buffer_200m.gpkg` |
| `LOW` | School geometry falls entirely outside the buffer |

The total feature count of the output layer `edu_facilities_flood_risk.gpkg` is identical to the input (475 features), confirming no features were lost during the join. This layer is the primary analytical deliverable and contains all original education facility attributes plus the `flood_risk` classification field.

### Step 3 - Subset extraction

Two filtered subsets were exported from `edu_facilities_flood_risk.gpkg` using **Processing Toolbox > Native > Extract by Attribute**:

- `schools_flood_prone.gpkg` — features where `flood_risk = 'HIGH'`
- `schools_safe.gpkg` — features where `flood_risk = 'LOW'`

### Step 4 - Symbology

In the QGIS project, `edu_facilities_flood_risk.gpkg` is symbolised using a categorised renderer on the `flood_risk` field:

| Value | Symbol | Colour |
|---|---|---|
| `HIGH` | Circle | Red |
| `LOW` | Circle | Green |

---

## Output layers

| File | Features | Description |
|---|---|---|
| `waterway_buffer_200m.gpkg` | 1 | Dissolved 200 m flood proximity corridor |
| `edu_facilities_flood_risk.gpkg` | 475 | Primary deliverable - all schools with `flood_risk` field |
| `schools_flood_prone.gpkg` | 50 | Schools classified `HIGH` flood risk |
| `schools_safe.gpkg` | 425 | Schools classified `LOW` flood risk |

---

## Key findings

- 475 education facilities were assessed across Sierra Leone.
- 50 schools (10.5%) fall within 200 m of a waterway and are classified as HIGH flood risk.
- 425 schools (89.5%) fall outside the flood proximity zone and are classified LOW.
- The 50 flood-exposed schools represent direct candidates for structural resilience interventions, elevated site assessments, and flood early-warning integration.

---

## Notes

- The `flood_risk` field uses a binary classification (HIGH/LOW) based solely on geometric proximity to the waterway buffer. It does not incorporate waterway flow volume, topographic elevation, or historical flood records. Further analysis combining DEM data would refine the exposure estimate.
- Task reference document: `Task1.docx`.
