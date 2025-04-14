# CDSE vs Planetary Computer I/O Benchmark

This repository contains a minimal benchmark comparing the **I/O performance** of Sentinel-2 data access across two major cloud platforms:

- **Microsoft Planetary Computer** (Azure Blob Storage)
- **Copernicus Data Space Ecosystem (CDSE)** (S3-based)

## 🔍 Goal

To measure how long it takes to read a small spatial subset (2 × 2 km) from a sequence of Sentinel-2 Level-2A images, and isolate the impact of **file format** and **data structure** on performance.

## 📁 Data & Formats

- **Sentinel-2 Green Band (B03)** from 10 products over MGRS tile `T32TMR`
- Formats tested:
  - Cloud-Optimized GeoTIFFs (`.tif`) from the Planetary Computer
  - JPEG 2000 (`.jp2`) from CDSE
  - Cloud-Optimized GeoTIFFs (`.tif`) from CDSE (Sentinel-1 mosaics, for control)

## 📊 Summary of Results

| Source               | Format        | Platform       | Time to read 10 assets |
|----------------------|---------------|----------------|-------------------------|
| Sentinel-2 (Green)   | COG (TIFF)    | Planetary      | ~2 seconds              |
| Sentinel-2 (Green)   | JPEG 2000     | CDSE           | ~87 seconds             |
| Sentinel-1 (VH)      | COG (TIFF)    | CDSE           | ~11 seconds             |

## ✅ Conclusion

The benchmark shows that **file format and internal encoding**, not cloud infrastructure, are the main determinants of read performance.

The poor performance of CDSE’s Sentinel-2 JPEG 2000 files is likely due to the **absence of the Tile-part Length Marker (TLM)**. Without TLMs, spatial subsetting triggers many redundant read operations. This issue is discussed in the [sentinel-2-jp2-tlm project](https://github.com/Kayrros/sentinel-2-jp2-tlm), which shows that adding TLM dramatically improves performance.

## 📓 Notebook

See the [benchmark notebook](./cdse_vs_planetary.ipynb) for full code, data access, visualizations, and interpretation.

