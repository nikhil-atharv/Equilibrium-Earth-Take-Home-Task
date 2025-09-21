# Python - Open Source Libraries

**eq_open_source_python.ipynb** file contains complete pipeline from accessing the data to final zonal statistics and exporting the results in python:

**Method employed for Resampling the 10m resolution sentinel - 2 L2A to 1m resolution**
1. Only four bands (B02, B03, B04, B08) are used as inpits for analysis to limit computation load on resampling
2. We have used function from rioxarray backed by rasterio:

      Resampled = xarray.DataArray.rio.reproject(
                                                   crs = 'EPSG:32644',
                                                   resolution = (1, 1)
                                                   resampling = Resampling.bilinear
                                                 )

3. The function Resample.bilinear is imported from rasterio.enums library. It is used when we need to sample this image at pixels that don't line up with the image's native grid. 'bilinear' - linear interpolation using the 4 nearest pixels (good for continuous data: reflectance, NDVI, temperature).
4. The function .reproject(crs, resolution, resampling) forces the image to be interpreted (and sampled) in that projection and at that scale. If the image's native resolution is coarser (e.g., Sentinel-2 10 m).


**Alternative Method for Super Resolution**

Use of s2dr3 model (Sentinel 2 Deep Resolution 3.0): A deep learning model from Google Alpha Earth Foundation. Which directly gives the MS image at given date and region (x, y).

Though we have employed normal resampling technique to upsample 10m sentinel image to 1m. The resulted tiffs can be found in Zip File link. 

https://drive.google.com/file/d/1Df9p1azzGXKoSdNvvHWQqaqe1YSt-gHS/view?usp=sharing 

**Limitations of Resampling**
1. Use of bilinear for continuous variables (reflectance, NDVI), nearest for categorical maps (land cover). Using bilinear on classes will produce non-integer, unrealistic values.
2. Performance/Cost: reprojecting to a much finer scale (e.g., from 10 m to 1 m across a large area) can be very expensive in computation and memory.
3. Range preservation: bilinear interpolation produces values within the range of the contributing pixels (no dramatic extrapolation), but smoothing is introduced.


**NDVI Threshold fixation**

Though it is convinient to select Standared Threshold as:

NDVI < 0.0 = water

NDVI 0.0 to 1.8 = Barren land

NDVI 1.8 to 3.0 = Sparse vegetation

NDVI > 3.0 = Dense vegetation


This varies according area of interest and density of vegetation presence. 

Hence threshold value = 0.21 was fixed for binary classification of NDVI. Based on following approach. 

The NDVI threshold values were established through an iterative and systematic process involving detailed pixel-level examination of NDVI layers. Multiple rounds of inspection were carried out to evaluate the spectral responses of different land cover types. A trial-and-error approach was employed, wherein provisional threshold ranges were applied, evaluated against reference observations, and subsequently refined. This iterative adjustment ensured that the final thresholds reliably distinguished the target classes while minimizing misclassification. The process was repeated multiple times to enhance the robustness and reproducibility of the selected threshold values

**NOTE**: The NDVI threshold values derived using the Google Earth Engine (GEE) approach and the PySTAC client vary due to differences in preprocessing steps, such as atmospheric correction, resampling methods, projection handling, and scaling of reflectance values, which can influence the computed NDVI distribution

**Outputs**
1. screening_results_python.geojson



