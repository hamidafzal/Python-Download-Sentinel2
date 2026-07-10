# Sentinel-2 Median Composite Downloader

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO/blob/main/S2_Median.ipynb)

A Google Earth Engine (GEE) + `geemap` notebook that downloads **cloud-masked Sentinel-2
Surface Reflectance median composites** for a custom Area of Interest (AOI), split into
user-defined multi-month periods, across a range of years.

> ⚠️ Replace `YOUR_USERNAME/YOUR_REPO` in the badge link above with your actual GitHub
> `username/repository` once you push this notebook, so the badge opens the correct file.

## What it does

For every year and every month-range you specify, the notebook:

1. **Filters** the [`COPERNICUS/S2_SR_HARMONIZED`](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S2_SR_HARMONIZED) collection by AOI, date range, and a maximum cloud percentage (< 20%).
2. **Masks clouds/cirrus** for every scene using the `QA60` band.
3. **Reduces** the filtered collection to a per-pixel **median** composite.
4. **Exports** each band as a separate GeoTIFF:
   - 10 m bands: `B2, B3, B4, B8` (Blue, Green, Red, NIR)
   - 20 m bands: `B5, B6, B7, B8A, B11, B12, QA60` (Red Edge, SWIR, cloud mask)
5. **Logs metadata** (source scene IDs and their cloud cover %) for every composite to a CSV file.

Output is organized as:
```
Download/
└── S2_<year>/
    └── <year>_<start-month>-<end-month>/
        ├── B2_median_img_<period>.tif
        ├── B3_median_img_<period>.tif
        ├── ...
        └── QA60_median_img_<period>.tif
Download/Sentinel_metadata.csv
```

## Requirements

- A free [Google Earth Engine account](https://code.earthengine.google.com/register)
- Python 3.10+ (only if running locally)
- `earthengine-api`, `geemap`, `pandas`

## Running it

### Option 1 — Google Colab (recommended, no local setup)
1. Click the **Open in Colab** badge above.
2. Run the first cell to install `earthengine-api` and `geemap`.
3. Run the authentication cell and follow the sign-in link.
4. Continue through the notebook top to bottom.

### Option 2 — Locally (Jupyter)
```bash
pip install earthengine-api geemap pandas
jupyter notebook S2_Median.ipynb
```
Then run the cells in order.

## Usage steps

1. **Authenticate & initialize Earth Engine** — run once per session.
2. **Define your Area of Interest (AOI)** — must be **≤ 250 km²**. Two options:
   - Draw a rectangle/polygon on the interactive map, then capture it as `roi`.
   - Paste an existing coordinate list into `ee.Geometry.Polygon(...)`.
3. **Set the year range** — edit `years = list(range(2017, 2025))`.
4. **Set the month intervals** — edit `months_intervals` (defaults to quarterly periods).
5. **Set the projection** — the export CRS defaults to `EPSG:32633` (UTM zone 33N).
   Change this to match your AOI's UTM zone.
6. **Run the export cell** — GeoTIFFs and the metadata CSV will be written to `~/Download`.

## Notes

- Composites with 0 matching images for a given period are automatically skipped.
- On Google Colab, the `~/Download` folder is on the **temporary VM disk** — download the
  results or copy them to Google Drive before your Colab session ends.
- Cloud filtering uses `CLOUDY_PIXEL_PERCENTAGE < 20`; adjust the threshold in the code if needed.

## License
 MIT
