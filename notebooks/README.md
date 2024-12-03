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

## [31 Site Map](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-31-site-map.ipynb)

Use redlining data from
[Mapping Inequality](https://dsl.richmond.edu/panorama/redlining)
to construct a site map for selected city.

- [redline.py](https://github.com/byandell-envsys/landmapy/blob/main/landmapy/redline.py)
  - `redline_gdf()`: Read redlining GeoDataFrame from Mapping Inequality.
  - `plot_redline()`: Plot overlay of redlining GeoDataFrame with state boundaries.
- Site map of selected city
  - `city = "Madison"`
  - `redlining_gdf[redlining_gdf.city == city].plot()`

## [91 Select Earthaccess Images](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-91-download-earthaccess.ipynb)

Search for cloud-free dates using
[NASA Worldview Site](https://worldview.earthdata.nasa.gov/):

1. Search the
[NASA Worldview Site](https://worldview.earthdata.nasa.gov/)
for selected city.
2. `Add Layer` for `HLSL30` product as a base map.
3. Dragging date indicator on bottom to month July, 2023, for a day with available data and little to no cloud cover over city.

- [earthaccess](https://earthaccess.readthedocs.io/en/)
  - `earthaccess.login()`:
  - `earthaccess.search_data()`:
  - `earthaccess.open()`: 

## [92 Process Earthaccess Images](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-92-bulk-download.ipynb)

This is a more advanced version of
[redlining-32-wrangle-multispectral.ipynb](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-32-wrangle-multispectral.ipynb)
with data coming from
[earthaccess](https://earthaccess.readthedocs.io/en/).

- [process.py](https://github.com/byandell-envsys/landmapy/blob/main/landmapy/process.py)
  - `process_image()`: Load, crop, and scale a raster image from earthaccess.
  - `process_cloud_mask()`: Load an 8-bit Fmask file and process to a boolean mask.
  - `process_metadata()`: Process raster data from earthaccess metadata.
  - `process_bands()`: Process bands from GeoDataFrame and metadata.

## [33 Spectral Indices](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-33-spectral-indices.ipynb)

Healthy vegetation reflects much _Near-InfraRed_ (`NIR`) radiation.
Less healthy vegetation reflects a similar amounts of the
visible light spectra, but less `NIR` radiation.
Little drop in `Green` radiation occurs until the plant is very stressed or dead.
Thus, `NIR` allows us to get ahead of what we can see with our eyes.

Different species of plants reflect different spectral signatures, but
the *pattern* of the signatures are similar. `NDVI` compares the amount of
NIR reflectance to the amount of Red reflectance, thus accounting for
many of the species differences and isolating the health of the plant.
The formula for calculating NDVI is:

```
NDVI = (NIR - Red) / (NIR + Red)
```

Read more about NDVI and other vegetation indices:

- [earthdatascience.org](https://www.earthdatascience.org/courses/use-data-open-source-python/multispectral-remote-sensing/vegetation-indices-in-python/calculate-NDVI-python/)
- [USGS](https://www.usgs.gov/landsat-missions/landsat-surface-reflectance-derived-spectral-indices)

1. Calculate `NDVI`.
2. Calculate another index. Common ones in this context might be
   - [NDMI](https://www.usgs.gov/landsat-missions/normalized-difference-moisture-index) (moisture)
   - [NDBaI](https://doi.org/10.1109/IGARSS.2005.1526319.) (bareness)
   - [NDBI](https://pro.arcgis.com/en/pro-app/3.3/arcpy/spatial-analyst/ndbi.htm) (built-up)


## [34 Plot](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-34-plot.ipynb)

#### Prepare to plot

Project to Mercator CRS.

1. Reproject your area of interest with .to_crs(ccrs.Mercator())
2. Reproject your NDVI and band raster data using `.rio.reproject(ccrs.Mercator())

#### Plot raster with overlay with xarray

Plotting raster and vector data together using `pandas` and `xarray` requires the `matplotlib.pyplot` library to access some plot layour tools.
Using the code below as a starting point, you can play around with adding:

1. Labels and titles
2. Different colors with cmap and edgecolor
3. Different line thickness with line_width

#### Plot raster with overlay with hvplot

Use `hvplot`.
Note that some parameter names are the same and some are different.
Do you notice any physical lines in the NDVI data that line up with the redlining boundaries?

#### Plot bands with linked subplots

Make a three-panel plot with Red, NIR, and Green bands.
Why do you think we aren’t using the green band to look at vegetation?

#### Plot RBG

Plot an RGB image using both `matplotlib` and `hvplot`.
It also performs an action called “Contrast stretching”, and brightens the image.

Read through the `stretch_rgb` function, and fill out the docstring with the rest of the parameters
and your own descriptions. 
Adjust the low, high, and brighten numbers until you are satisfied with the image.

#### Plot CIR

Plot a false color RGB image. CIR images have the following bands:

- red becomes NIR
- green becomes red
- blue becomes green

#### Adjust the levels

The NIR band in this image is very bright.
Can you adjust it so it is balanced more effectively by the other bands?

## [41 Calculate Zonal Statistics](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-41-zonal-stats.ipynb)

- Convert your vector data to a raster mask using the `regionmask` package.
  - Plot redlining gdf to see CRS
  - Plot the `ndvi_da` to see CRS
  - Perform `regionmask` and plot
- Calculate `zonal_stats()`
- Plot regional statistics
  - Merge `NDVI` values into the redlining `GeoDataFrame`
  - Convert `grade` column (`str` or `object` type) to an ordered `pd.Categorical` type.
  - Drop all `NA` grade values.
  - Plot the `NDVI` and redlining grade next to each other in linked subplots.

## [42 Fit a Model](https://github.com/earthlab-education/fundamentals-04-redlining-byandell/blob/main/notebooks/redlining-42-tree-model.ipynb)

Assess if redlining is related to `NDVI`:
how well can predict redlining `grade` from mean `NDVI` value?
With 4 categories, any accuracy >25% suggests a relationship
between vegetation health and redlining.

- Fit a Decision Tree Classifier
  - It makes sense to split up into squares by thresholds since
we expect thresholds in mean `NDVI` to indicate redlining grade.
For practical reasons, limit tree split to depth of 2.
- Plot Model Results
  - Predict grades for each region using the `.predict()` method of your `DecisionTreeClassifier`.
  - Subtract the actual grades from the predicted grades.
  - Plot the calculated prediction errors as a chloropleth.
- Evaluate Model with CV
  - Cross-validation gives range of potential accuracies using subset of data.

- [sklearn](https://scikit-learn.org/stable/)
  - [tree.DecisionTreeClassifier()](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html)
  - [tree.plot_tree()](https://scikit-learn.org/stable/modules/generated/sklearn.tree.plot_tree.html)
  - [model_selection.train_test_split()](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)
  - [model_selectioncross_val_score()](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.cross_val_score.html)
