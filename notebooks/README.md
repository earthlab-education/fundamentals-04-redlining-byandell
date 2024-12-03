# README: Notebook Workflows

This workflow leaves out one-time tasks, such as setting up data directory,
and details of python module imports.
Highlighted script refers to modules being developed in
<https://github.com/byandell-envsys/landmapy>.
Goal here is to use this pseudo-code outline to guide
development of `landmapy` for use in
[madison.ipynb](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/madison.ipynb)
and the EDA final project on
[habitatSuitability](https://github.com/byandell-envsys/habitatSuitability).

### 31 Site Map

Use redlining data from
[Mapping Inequality](https://dsl.richmond.edu/panorama/redlining)

- [redline.py](https://github.com/byandell-envsys/landmapy/blob/main/landmapy/redline.py)
  - `redline_gdf()`: Read redlining GeoDataFrame from Mapping Inequality.
  - `plot_redline()`: Plot overlay of redlining GeoDataFrame with state boundaries.
- Site map of selected city
  - `city = "Madison"`
  - `redlining_gdf[redlining_gdf.city == city].plot()`

### 92 Process Earthaccess Images

- [process.py](https://github.com/byandell-envsys/landmapy/blob/main/landmapy/process.py)
  - `process_image()`: Load, crop, and scale a raster image from earthaccess.
  - `process_cloud_mask()`: Load an 8-bit Fmask file and process to a boolean mask.
  - `process_metadata()`: Process raster data from earthaccess metadata.
  - `process_bands()`: Process bands from GeoDataFrame and metadata.
