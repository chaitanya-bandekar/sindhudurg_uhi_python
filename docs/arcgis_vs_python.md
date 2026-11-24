# How this version corrects the earlier ArcGIS analysis

Re-run from the original USGS Collection-2 files with `uhi_pipeline.py`. Grid: 30 m UTM 43N, clipped to `Sindhudurga_District.shp`.
Masked with QA_PIXEL: fill, cloud, dilated cloud, cirrus, shadow and water removed.

## Scenes actually used

| Epoch | Scene | Date | Notes |
|---|---|---|---|
| 2000 | Landsat 5 TM, L1TP 147/049 | 29 Mar 2000 | Level-1 only, so it can't be compared with the later years |
| 2010 | Landsat 7 ETM+, L2SP 147/049 | 2 Apr 2010 | SLC-off: only 72.7 % of the district has data |
| 2020 | Landsat 8, L2SP 147/049 | 20 Mar 2020 | Earliest date in the season |
| 2026 | **Landsat 9**, L2SP 147/049 | **14 Apr 2026** | ArcGIS band statistics match this scene exactly, not the Landsat 8 22 Apr scene |

## Side-by-side

| Quantity | Original (ArcGIS / report) | Re-run (corrected) | What caused the difference |
|---|---|---|---|
| NDVI 2010 / 2020 / 2026 | 0.187 / 0.226 / 0.228 | **0.442 / 0.539 / 0.541** | NDVI was computed on raw DN, without the ×0.0000275 − 0.2 scaling. The re-run reproduces the original values exactly (0.187 / 0.226 / 0.228) when that scaling is left out. |
| NDVI 2000 | 0.164 | 0.258 (TOA) | Raw 0–255 DN. Even top-of-atmosphere NDVI can't be compared with surface reflectance. |
| NDBI 2010 / 2026 | 0.016 / −0.024 | 0.025 / −0.051 | Same raw-DN issue |
| LST 2010 mean | 41.02 °C | 41.45 °C | The original removed values outside 15–50 °C **in 2010 only** (reproduced: 41.02). 2020 and 2026 were not clipped. |
| LST 2026 mean | 42.97 °C | 43.05 °C | No masking in the original (reproduced: 42.97) |
| LST 2000 | 29.06 °C | 30.41 °C (BT 29.13) | The original value is brightness temperature, with no emissivity correction applied |
| **LST change 2010→2026** | +2.1 °C (quoted) / 1.95 (from table) | **+2.08 °C** | Correct method: mean of the difference over pixels that are clear in **both** years |
| LST change 2010→2020 | not reported | −0.51 °C | 2020 is a 20 March scene, so it should not be read as cooling |
| LST change, 14→22 Apr 2026 (L9 vs L8) | not checked | **+0.08 °C** | Little temperature drift within April, so the 12-day 2010/2026 date gap is unlikely to explain the warming |
| NDVI change 2010→2026 | "rising" | +0.090 (2010→20: +0.087; 2020→26: **+0.005**) | Greening, then a plateau after 2020 |
| NDBI change 2010→2026 | "balanced around zero" | **−0.064** (systematic decrease) | The report's statement doesn't hold with correctly scaled data |
| RF R² | 0.701 (all pixels, includes training) | **0.72 test**, spatial 5 km blocks (train 0.86) | Proper held-out accuracy: 22,658 training and 7,342 test samples |
| RF RMSE | 3.16 °C | **2.99 °C** (test) | |
| RF importance | NDBI 52 / NDVI 34 / DEM 14 % | NDBI 68 / NDVI 20 / DEM 12 % (impurity); NDBI 0.68 / NDVI 0.31 / DEM 0.14 (permutation, test) | NDBI is still dominant. NDVI and NDBI correlate at r = −0.92, so state this as a caveat. |
| Hotspot threshold | 48.74 °C | 48.78 °C | |
| Hotspot area | 833.6 km², 16.0 % | **826.4 km², 16.6 % of clear land** (16.2 % of polygon) | Cloud and water masked. The original used a hard-coded 5,207 km² area; the polygon is 5,105 km². |

## Bottom line
The main findings hold: about +2.1 °C warming over 2010–2026 on common pixels, NDBI as the strongest predictor, and about 16 % of the district as hotspots.
What changes: every NDVI and NDBI value, the RF accuracy statement, the NDBI discussion, the scene and date table, and how the 2000 and 2020 epochs are described.
