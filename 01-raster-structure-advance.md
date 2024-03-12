---
title: Intro to Raster Data (Advance)
teaching: 30
exercises: 20
source: Rmd
---


```{.warning}
Warning in
download.file("https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m/physical/ne_110m_graticules_all.zip",
: cannot open URL
'https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m/physical/ne_110m_graticules_all.zip':
HTTP status was '500 Internal Server Error'
```

```{.error}
Error in download.file("https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m/physical/ne_110m_graticules_all.zip", : cannot open URL 'https://www.naturalearthdata.com/http//www.naturalearthdata.com/download/110m/physical/ne_110m_graticules_all.zip'
```

::::::::::::::::::::::::::::::::::::::: objectives

- Explore raster attributes and metadata using R.
- Describe the difference between single- and multi-band rasters.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- What is a raster dataset?
- How can I handle missing or bad data values for a raster?

::::::::::::::::::::::::::::::::::::::::::::::::::




```r
library(terra)
library(ggplot2)
library(dplyr)
```

## View Raster File Attributes

We will be working with a series of GeoTIFF files in this lesson. The
GeoTIFF format contains a set of embedded tags with metadata about the raster
data. We can use the function `describe()` to get information about our raster
data before we read that data into R. It is ideal to do this before importing
your data.


```r
describe("data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_dsmCrop.tif")
```

```{.output}
 [1] "Driver: GTiff/GeoTIFF"                                                                                                                                                                                                                                                         
 [2] "Files: data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_dsmCrop.tif"                                                                                                                                                                                                         
 [3] "Size is 1697, 1367"                                                                                                                                                                                                                                                            
 [4] "Coordinate System is:"                                                                                                                                                                                                                                                         
 [5] "PROJCRS[\"WGS 84 / UTM zone 18N\","                                                                                                                                                                                                                                            
 [6] "    BASEGEOGCRS[\"WGS 84\","                                                                                                                                                                                                                                                   
 [7] "        DATUM[\"World Geodetic System 1984\","                                                                                                                                                                                                                                 
 [8] "            ELLIPSOID[\"WGS 84\",6378137,298.257223563,"                                                                                                                                                                                                                       
 [9] "                LENGTHUNIT[\"metre\",1]]],"                                                                                                                                                                                                                                    
[10] "        PRIMEM[\"Greenwich\",0,"                                                                                                                                                                                                                                               
[11] "            ANGLEUNIT[\"degree\",0.0174532925199433]],"                                                                                                                                                                                                                        
[12] "        ID[\"EPSG\",4326]],"                                                                                                                                                                                                                                                   
[13] "    CONVERSION[\"UTM zone 18N\","                                                                                                                                                                                                                                              
[14] "        METHOD[\"Transverse Mercator\","                                                                                                                                                                                                                                       
[15] "            ID[\"EPSG\",9807]],"                                                                                                                                                                                                                                               
[16] "        PARAMETER[\"Latitude of natural origin\",0,"                                                                                                                                                                                                                           
[17] "            ANGLEUNIT[\"degree\",0.0174532925199433],"                                                                                                                                                                                                                         
[18] "            ID[\"EPSG\",8801]],"                                                                                                                                                                                                                                               
[19] "        PARAMETER[\"Longitude of natural origin\",-75,"                                                                                                                                                                                                                        
[20] "            ANGLEUNIT[\"degree\",0.0174532925199433],"                                                                                                                                                                                                                         
[21] "            ID[\"EPSG\",8802]],"                                                                                                                                                                                                                                               
[22] "        PARAMETER[\"Scale factor at natural origin\",0.9996,"                                                                                                                                                                                                                  
[23] "            SCALEUNIT[\"unity\",1],"                                                                                                                                                                                                                                           
[24] "            ID[\"EPSG\",8805]],"                                                                                                                                                                                                                                               
[25] "        PARAMETER[\"False easting\",500000,"                                                                                                                                                                                                                                   
[26] "            LENGTHUNIT[\"metre\",1],"                                                                                                                                                                                                                                          
[27] "            ID[\"EPSG\",8806]],"                                                                                                                                                                                                                                               
[28] "        PARAMETER[\"False northing\",0,"                                                                                                                                                                                                                                       
[29] "            LENGTHUNIT[\"metre\",1],"                                                                                                                                                                                                                                          
[30] "            ID[\"EPSG\",8807]]],"                                                                                                                                                                                                                                              
[31] "    CS[Cartesian,2],"                                                                                                                                                                                                                                                          
[32] "        AXIS[\"(E)\",east,"                                                                                                                                                                                                                                                    
[33] "            ORDER[1],"                                                                                                                                                                                                                                                         
[34] "            LENGTHUNIT[\"metre\",1]],"                                                                                                                                                                                                                                         
[35] "        AXIS[\"(N)\",north,"                                                                                                                                                                                                                                                   
[36] "            ORDER[2],"                                                                                                                                                                                                                                                         
[37] "            LENGTHUNIT[\"metre\",1]],"                                                                                                                                                                                                                                         
[38] "    USAGE["                                                                                                                                                                                                                                                                    
[39] "        SCOPE[\"Engineering survey, topographic mapping.\"],"                                                                                                                                                                                                                  
[40] "        AREA[\"Between 78°W and 72°W, northern hemisphere between equator and 84°N, onshore and offshore. Bahamas. Canada - Nunavut; Ontario; Quebec. Colombia. Cuba. Ecuador. Greenland. Haiti. Jamica. Panama. Turks and Caicos Islands. United States (USA). Venezuela.\"],"
[41] "        BBOX[0,-78,84,-72]],"                                                                                                                                                                                                                                                  
[42] "    ID[\"EPSG\",32618]]"                                                                                                                                                                                                                                                       
[43] "Data axis to CRS axis mapping: 1,2"                                                                                                                                                                                                                                            
[44] "Origin = (731453.000000000000000,4713838.000000000000000)"                                                                                                                                                                                                                     
[45] "Pixel Size = (1.000000000000000,-1.000000000000000)"                                                                                                                                                                                                                           
[46] "Metadata:"                                                                                                                                                                                                                                                                     
[47] "  AREA_OR_POINT=Area"                                                                                                                                                                                                                                                          
[48] "Image Structure Metadata:"                                                                                                                                                                                                                                                     
[49] "  COMPRESSION=LZW"                                                                                                                                                                                                                                                             
[50] "  INTERLEAVE=BAND"                                                                                                                                                                                                                                                             
[51] "Corner Coordinates:"                                                                                                                                                                                                                                                           
[52] "Upper Left  (  731453.000, 4713838.000) ( 72d10'52.71\"W, 42d32'32.18\"N)"                                                                                                                                                                                                     
[53] "Lower Left  (  731453.000, 4712471.000) ( 72d10'54.71\"W, 42d31'47.92\"N)"                                                                                                                                                                                                     
[54] "Upper Right (  733150.000, 4713838.000) ( 72d 9'38.40\"W, 42d32'30.35\"N)"                                                                                                                                                                                                     
[55] "Lower Right (  733150.000, 4712471.000) ( 72d 9'40.41\"W, 42d31'46.08\"N)"                                                                                                                                                                                                     
[56] "Center      (  732301.500, 4713154.500) ( 72d10'16.56\"W, 42d32' 9.13\"N)"                                                                                                                                                                                                     
[57] "Band 1 Block=1697x1 Type=Float64, ColorInterp=Gray"                                                                                                                                                                                                                            
[58] "  Min=305.070 Max=416.070 "                                                                                                                                                                                                                                                    
[59] "  Minimum=305.070, Maximum=416.070, Mean=359.853, StdDev=17.832"                                                                                                                                                                                                               
[60] "  NoData Value=-9999"                                                                                                                                                                                                                                                          
[61] "  Metadata:"                                                                                                                                                                                                                                                                   
[62] "    STATISTICS_MAXIMUM=416.06997680664"                                                                                                                                                                                                                                        
[63] "    STATISTICS_MEAN=359.85311802914"                                                                                                                                                                                                                                           
[64] "    STATISTICS_MINIMUM=305.07000732422"                                                                                                                                                                                                                                        
[65] "    STATISTICS_STDDEV=17.83169335933"                                                                                                                                                                                                                                          
```

If you wish to store this information in R, you can do the following:


```r
HARV_dsmCrop_info <- capture.output(
  describe("data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_dsmCrop.tif")
)
```

Each line of text that was printed to the console is now stored as an element of
the character vector `HARV_dsmCrop_info`. We will be exploring this data throughout this
episode. By the end of this episode, you will be able to explain and understand the output above.






## Raster Bands

The Digital Surface Model object (`DSM_HARV`) that we've been working with is a
single band raster. This means that there is only one dataset stored in the
raster: surface elevation in meters for one time period.

![](fig/dc-spatial-raster/single_multi_raster.png){alt='Multi-band raster image'}

A raster dataset can contain one or more bands. We can use the `rast()`
function to import one single band from a single or multi-band raster. We can
view the number of bands in a raster using the `nly()` function.


```r
nlyr(DSM_HARV)
```

```{.output}
[1] 1
```

However, raster data can also be multi-band, meaning that one raster file
contains data for more than one variable or time period for each cell. By
default the `raster()` function only imports the first band in a raster
regardless of whether it has one or more bands. Jump to a later episode in
this series for information on working with multi-band rasters:
[Work with Multi-band Rasters in R](05-raster-multi-band-in-r/).

## Dealing with Missing Data

Raster data often has a `NoDataValue` associated with it. This is a value
assigned to pixels where data is missing or no data were collected.

By default the shape of a raster is always rectangular. So if we have  a dataset
that has a shape that isn't rectangular, some pixels at the edge of the raster
will have `NoDataValue`s. This often happens when the data were collected by an
airplane which only flew over some part of a defined region.

In the image below, the pixels that are black have `NoDataValue`s. The camera
did not collect data in these areas.


```{.output}
The legacy packages maptools, rgdal, and rgeos, underpinning the sp package,
which was just loaded, will retire in October 2023.
Please refer to R-spatial evolution reports for details, especially
https://r-spatial.org/r/2023/05/15/evolution4.html.
It may be desirable to make the sf package available;
package maintainers should consider adding sf to Suggests:.
The sp package is now running under evolution status 2
     (status 2 uses the sf package in place of rgdal)
```

<img src="fig/01-raster-structure-advance-rendered-demonstrate-no-data-black-ggplot-1.png" style="display: block; margin: auto;" />

In the next image, the black edges have been assigned `NoDataValue`. R doesn't
render pixels that contain a specified `NoDataValue`. R assigns missing data
with the `NoDataValue` as `NA`.

The difference here shows up as ragged edges on the plot, rather than black
spaces where there is no data.

<img src="fig/01-raster-structure-advance-rendered-demonstrate-no-data-ggplot-1.png" style="display: block; margin: auto;" />

If your raster already has `NA` values set correctly but you aren't sure where 
they are, you can deliberately plot them in a particular colour. This can be 
useful when checking a dataset's coverage. For instance, sometimes data can be 
missing where a sensor could not 'see' its target data, and you may wish to 
locate that missing data and fill it in.

To highlight `NA` values in ggplot, alter the `scale_fill_*()` layer to contain 
a colour instruction for `NA` values, like `scale_fill_viridis_c(na.value = 'deeppink')`

<img src="fig/01-raster-structure-advance-rendered-unnamed-chunk-4-1.png" style="display: block; margin: auto;" />

The value that is conventionally used to take note of missing data (the
`NoDataValue` value) varies by the raster data type. For floating-point rasters,
the figure `-3.4e+38` is a common default, and for integers, `-9999` is
common. Some disciplines have specific conventions that vary from these
common values.

In some cases, other `NA` values may be more appropriate. An `NA` value should
be a) outside the range of valid values, and b) a value that fits the data type
in use. For instance, if your data ranges continuously from -20 to 100, 0 is
not an acceptable `NA` value! Or, for categories that number 1-15, 0 might be
fine for `NA`, but using -.000003 will force you to save the GeoTIFF on disk
as a floating point raster, resulting in a bigger file.

If we are lucky, our GeoTIFF file has a tag that tells us what is the
`NoDataValue`. If we are less lucky, we can find that information in the
raster's metadata. If a `NoDataValue` was stored in the GeoTIFF tag, when R
opens up the raster, it will assign each instance of the value to `NA`. Values
of `NA` will be ignored by R as demonstrated above.

:::::::::::::::::::::::::::::::::::::::::  challenge

## Challenge

Use the output from the `describe()` and `sources()` functions to find out what 
`NoDataValue` is used for our `DSM_HARV` dataset.

:::::::::::::::  solution

## Answers


```r
describe(sources(DSM_HARV))
```

```{.output}
 [1] "Driver: GTiff/GeoTIFF"                                                                                                                                                                                                                                                         
 [2] "Files: /home/runner/work/DTRA_workshop_GIS/DTRA_workshop_GIS/site/built/data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_dsmCrop.tif"                                                                                                                                        
 [3] "Size is 1697, 1367"                                                                                                                                                                                                                                                            
 [4] "Coordinate System is:"                                                                                                                                                                                                                                                         
 [5] "PROJCRS[\"WGS 84 / UTM zone 18N\","                                                                                                                                                                                                                                            
 [6] "    BASEGEOGCRS[\"WGS 84\","                                                                                                                                                                                                                                                   
 [7] "        DATUM[\"World Geodetic System 1984\","                                                                                                                                                                                                                                 
 [8] "            ELLIPSOID[\"WGS 84\",6378137,298.257223563,"                                                                                                                                                                                                                       
 [9] "                LENGTHUNIT[\"metre\",1]]],"                                                                                                                                                                                                                                    
[10] "        PRIMEM[\"Greenwich\",0,"                                                                                                                                                                                                                                               
[11] "            ANGLEUNIT[\"degree\",0.0174532925199433]],"                                                                                                                                                                                                                        
[12] "        ID[\"EPSG\",4326]],"                                                                                                                                                                                                                                                   
[13] "    CONVERSION[\"UTM zone 18N\","                                                                                                                                                                                                                                              
[14] "        METHOD[\"Transverse Mercator\","                                                                                                                                                                                                                                       
[15] "            ID[\"EPSG\",9807]],"                                                                                                                                                                                                                                               
[16] "        PARAMETER[\"Latitude of natural origin\",0,"                                                                                                                                                                                                                           
[17] "            ANGLEUNIT[\"degree\",0.0174532925199433],"                                                                                                                                                                                                                         
[18] "            ID[\"EPSG\",8801]],"                                                                                                                                                                                                                                               
[19] "        PARAMETER[\"Longitude of natural origin\",-75,"                                                                                                                                                                                                                        
[20] "            ANGLEUNIT[\"degree\",0.0174532925199433],"                                                                                                                                                                                                                         
[21] "            ID[\"EPSG\",8802]],"                                                                                                                                                                                                                                               
[22] "        PARAMETER[\"Scale factor at natural origin\",0.9996,"                                                                                                                                                                                                                  
[23] "            SCALEUNIT[\"unity\",1],"                                                                                                                                                                                                                                           
[24] "            ID[\"EPSG\",8805]],"                                                                                                                                                                                                                                               
[25] "        PARAMETER[\"False easting\",500000,"                                                                                                                                                                                                                                   
[26] "            LENGTHUNIT[\"metre\",1],"                                                                                                                                                                                                                                          
[27] "            ID[\"EPSG\",8806]],"                                                                                                                                                                                                                                               
[28] "        PARAMETER[\"False northing\",0,"                                                                                                                                                                                                                                       
[29] "            LENGTHUNIT[\"metre\",1],"                                                                                                                                                                                                                                          
[30] "            ID[\"EPSG\",8807]]],"                                                                                                                                                                                                                                              
[31] "    CS[Cartesian,2],"                                                                                                                                                                                                                                                          
[32] "        AXIS[\"(E)\",east,"                                                                                                                                                                                                                                                    
[33] "            ORDER[1],"                                                                                                                                                                                                                                                         
[34] "            LENGTHUNIT[\"metre\",1]],"                                                                                                                                                                                                                                         
[35] "        AXIS[\"(N)\",north,"                                                                                                                                                                                                                                                   
[36] "            ORDER[2],"                                                                                                                                                                                                                                                         
[37] "            LENGTHUNIT[\"metre\",1]],"                                                                                                                                                                                                                                         
[38] "    USAGE["                                                                                                                                                                                                                                                                    
[39] "        SCOPE[\"Engineering survey, topographic mapping.\"],"                                                                                                                                                                                                                  
[40] "        AREA[\"Between 78°W and 72°W, northern hemisphere between equator and 84°N, onshore and offshore. Bahamas. Canada - Nunavut; Ontario; Quebec. Colombia. Cuba. Ecuador. Greenland. Haiti. Jamica. Panama. Turks and Caicos Islands. United States (USA). Venezuela.\"],"
[41] "        BBOX[0,-78,84,-72]],"                                                                                                                                                                                                                                                  
[42] "    ID[\"EPSG\",32618]]"                                                                                                                                                                                                                                                       
[43] "Data axis to CRS axis mapping: 1,2"                                                                                                                                                                                                                                            
[44] "Origin = (731453.000000000000000,4713838.000000000000000)"                                                                                                                                                                                                                     
[45] "Pixel Size = (1.000000000000000,-1.000000000000000)"                                                                                                                                                                                                                           
[46] "Metadata:"                                                                                                                                                                                                                                                                     
[47] "  AREA_OR_POINT=Area"                                                                                                                                                                                                                                                          
[48] "Image Structure Metadata:"                                                                                                                                                                                                                                                     
[49] "  COMPRESSION=LZW"                                                                                                                                                                                                                                                             
[50] "  INTERLEAVE=BAND"                                                                                                                                                                                                                                                             
[51] "Corner Coordinates:"                                                                                                                                                                                                                                                           
[52] "Upper Left  (  731453.000, 4713838.000) ( 72d10'52.71\"W, 42d32'32.18\"N)"                                                                                                                                                                                                     
[53] "Lower Left  (  731453.000, 4712471.000) ( 72d10'54.71\"W, 42d31'47.92\"N)"                                                                                                                                                                                                     
[54] "Upper Right (  733150.000, 4713838.000) ( 72d 9'38.40\"W, 42d32'30.35\"N)"                                                                                                                                                                                                     
[55] "Lower Right (  733150.000, 4712471.000) ( 72d 9'40.41\"W, 42d31'46.08\"N)"                                                                                                                                                                                                     
[56] "Center      (  732301.500, 4713154.500) ( 72d10'16.56\"W, 42d32' 9.13\"N)"                                                                                                                                                                                                     
[57] "Band 1 Block=1697x1 Type=Float64, ColorInterp=Gray"                                                                                                                                                                                                                            
[58] "  Min=305.070 Max=416.070 "                                                                                                                                                                                                                                                    
[59] "  Minimum=305.070, Maximum=416.070, Mean=359.853, StdDev=17.832"                                                                                                                                                                                                               
[60] "  NoData Value=-9999"                                                                                                                                                                                                                                                          
[61] "  Metadata:"                                                                                                                                                                                                                                                                   
[62] "    STATISTICS_MAXIMUM=416.06997680664"                                                                                                                                                                                                                                        
[63] "    STATISTICS_MEAN=359.85311802914"                                                                                                                                                                                                                                           
[64] "    STATISTICS_MINIMUM=305.07000732422"                                                                                                                                                                                                                                        
[65] "    STATISTICS_STDDEV=17.83169335933"                                                                                                                                                                                                                                          
```

`NoDataValue` are encoded as -9999.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Bad Data Values in Rasters

Bad data values are different from `NoDataValue`s. Bad data values are values
that fall outside of the applicable range of a dataset.

Examples of Bad Data Values:

- The normalized difference vegetation index (NDVI), which is a measure of
  greenness, has a valid range of -1 to 1. Any value outside of that range would
  be considered a "bad" or miscalculated value.
- Reflectance data in an image will often range from 0-1 or 0-10,000 depending
  upon how the data are scaled. Thus a value greater than 1 or greater than 10,000
  is likely caused by an error in either data collection or processing.

### Find Bad Data Values

Sometimes a raster's metadata will tell us the range of expected values for a
raster. Values outside of this range are suspect and we need to consider that
when we analyze the data. Sometimes, we need to use some common sense and
scientific insight as we examine the data - just as we would for field data to
identify questionable values.

Plotting data with appropriate highlighting can help reveal patterns in bad
values and may suggest a solution. Below, reclassification is used to highlight
elevation values over 400m with a contrasting colour.

<img src="fig/01-raster-structure-advance-rendered-demo-bad-data-highlighting-1.png" style="display: block; margin: auto;" />

## Create A Histogram of Raster Values

We can explore the distribution of values contained within our raster using the
`geom_histogram()` function which produces a histogram. Histograms are often
useful in identifying outliers and bad data values in our raster data.


```r
ggplot() +
    geom_histogram(data = DSM_HARV_df, aes(HARV_dsmCrop))
```

```{.output}
`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="fig/01-raster-structure-advance-rendered-view-raster-histogram-1.png" style="display: block; margin: auto;" />

Notice that a warning message is thrown when R creates the histogram.

`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

This warning is caused by a default setting in `geom_histogram` enforcing that there are
30 bins for the data. We can define the number of bins we want in the histogram
by using the `bins` value in the `geom_histogram()` function.


```r
ggplot() +
    geom_histogram(data = DSM_HARV_df, aes(HARV_dsmCrop), bins = 40)
```

<img src="fig/01-raster-structure-advance-rendered-view-raster-histogram2-1.png" style="display: block; margin: auto;" />

Note that the shape of this histogram looks similar to the previous one that
was created using the default of 30 bins. The distribution of elevation values
for our `Digital Surface Model (DSM)` looks reasonable. It is likely there are
no bad data values in this particular raster.

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge: Explore Raster Metadata

Use `describe()` to determine the following about the `NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_DSMhill.tif` file:

1. Does this file have the same CRS as `DSM_HARV`?
2. What is the `NoDataValue`?
3. What is resolution of the raster data?
4. How large would a 5x5 pixel area be on the Earth's surface?
5. Is the file a multi- or single-band raster?

Notice: this file is a hillshade. We will learn about hillshades in the [Working with Multi-band Rasters in R](05-raster-multi-band-in-r/) episode.

:::::::::::::::  solution

## Answers


```r
describe("data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_DSMhill.tif")
```

```{.output}
 [1] "Driver: GTiff/GeoTIFF"                                                                                                                                                                                                                                                         
 [2] "Files: data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_DSMhill.tif"                                                                                                                                                                                                         
 [3] "Size is 1697, 1367"                                                                                                                                                                                                                                                            
 [4] "Coordinate System is:"                                                                                                                                                                                                                                                         
 [5] "PROJCRS[\"WGS 84 / UTM zone 18N\","                                                                                                                                                                                                                                            
 [6] "    BASEGEOGCRS[\"WGS 84\","                                                                                                                                                                                                                                                   
 [7] "        DATUM[\"World Geodetic System 1984\","                                                                                                                                                                                                                                 
 [8] "            ELLIPSOID[\"WGS 84\",6378137,298.257223563,"                                                                                                                                                                                                                       
 [9] "                LENGTHUNIT[\"metre\",1]]],"                                                                                                                                                                                                                                    
[10] "        PRIMEM[\"Greenwich\",0,"                                                                                                                                                                                                                                               
[11] "            ANGLEUNIT[\"degree\",0.0174532925199433]],"                                                                                                                                                                                                                        
[12] "        ID[\"EPSG\",4326]],"                                                                                                                                                                                                                                                   
[13] "    CONVERSION[\"UTM zone 18N\","                                                                                                                                                                                                                                              
[14] "        METHOD[\"Transverse Mercator\","                                                                                                                                                                                                                                       
[15] "            ID[\"EPSG\",9807]],"                                                                                                                                                                                                                                               
[16] "        PARAMETER[\"Latitude of natural origin\",0,"                                                                                                                                                                                                                           
[17] "            ANGLEUNIT[\"degree\",0.0174532925199433],"                                                                                                                                                                                                                         
[18] "            ID[\"EPSG\",8801]],"                                                                                                                                                                                                                                               
[19] "        PARAMETER[\"Longitude of natural origin\",-75,"                                                                                                                                                                                                                        
[20] "            ANGLEUNIT[\"degree\",0.0174532925199433],"                                                                                                                                                                                                                         
[21] "            ID[\"EPSG\",8802]],"                                                                                                                                                                                                                                               
[22] "        PARAMETER[\"Scale factor at natural origin\",0.9996,"                                                                                                                                                                                                                  
[23] "            SCALEUNIT[\"unity\",1],"                                                                                                                                                                                                                                           
[24] "            ID[\"EPSG\",8805]],"                                                                                                                                                                                                                                               
[25] "        PARAMETER[\"False easting\",500000,"                                                                                                                                                                                                                                   
[26] "            LENGTHUNIT[\"metre\",1],"                                                                                                                                                                                                                                          
[27] "            ID[\"EPSG\",8806]],"                                                                                                                                                                                                                                               
[28] "        PARAMETER[\"False northing\",0,"                                                                                                                                                                                                                                       
[29] "            LENGTHUNIT[\"metre\",1],"                                                                                                                                                                                                                                          
[30] "            ID[\"EPSG\",8807]]],"                                                                                                                                                                                                                                              
[31] "    CS[Cartesian,2],"                                                                                                                                                                                                                                                          
[32] "        AXIS[\"(E)\",east,"                                                                                                                                                                                                                                                    
[33] "            ORDER[1],"                                                                                                                                                                                                                                                         
[34] "            LENGTHUNIT[\"metre\",1]],"                                                                                                                                                                                                                                         
[35] "        AXIS[\"(N)\",north,"                                                                                                                                                                                                                                                   
[36] "            ORDER[2],"                                                                                                                                                                                                                                                         
[37] "            LENGTHUNIT[\"metre\",1]],"                                                                                                                                                                                                                                         
[38] "    USAGE["                                                                                                                                                                                                                                                                    
[39] "        SCOPE[\"Engineering survey, topographic mapping.\"],"                                                                                                                                                                                                                  
[40] "        AREA[\"Between 78°W and 72°W, northern hemisphere between equator and 84°N, onshore and offshore. Bahamas. Canada - Nunavut; Ontario; Quebec. Colombia. Cuba. Ecuador. Greenland. Haiti. Jamica. Panama. Turks and Caicos Islands. United States (USA). Venezuela.\"],"
[41] "        BBOX[0,-78,84,-72]],"                                                                                                                                                                                                                                                  
[42] "    ID[\"EPSG\",32618]]"                                                                                                                                                                                                                                                       
[43] "Data axis to CRS axis mapping: 1,2"                                                                                                                                                                                                                                            
[44] "Origin = (731453.000000000000000,4713838.000000000000000)"                                                                                                                                                                                                                     
[45] "Pixel Size = (1.000000000000000,-1.000000000000000)"                                                                                                                                                                                                                           
[46] "Metadata:"                                                                                                                                                                                                                                                                     
[47] "  AREA_OR_POINT=Area"                                                                                                                                                                                                                                                          
[48] "Image Structure Metadata:"                                                                                                                                                                                                                                                     
[49] "  COMPRESSION=LZW"                                                                                                                                                                                                                                                             
[50] "  INTERLEAVE=BAND"                                                                                                                                                                                                                                                             
[51] "Corner Coordinates:"                                                                                                                                                                                                                                                           
[52] "Upper Left  (  731453.000, 4713838.000) ( 72d10'52.71\"W, 42d32'32.18\"N)"                                                                                                                                                                                                     
[53] "Lower Left  (  731453.000, 4712471.000) ( 72d10'54.71\"W, 42d31'47.92\"N)"                                                                                                                                                                                                     
[54] "Upper Right (  733150.000, 4713838.000) ( 72d 9'38.40\"W, 42d32'30.35\"N)"                                                                                                                                                                                                     
[55] "Lower Right (  733150.000, 4712471.000) ( 72d 9'40.41\"W, 42d31'46.08\"N)"                                                                                                                                                                                                     
[56] "Center      (  732301.500, 4713154.500) ( 72d10'16.56\"W, 42d32' 9.13\"N)"                                                                                                                                                                                                     
[57] "Band 1 Block=1697x1 Type=Float64, ColorInterp=Gray"                                                                                                                                                                                                                            
[58] "  Min=-0.714 Max=1.000 "                                                                                                                                                                                                                                                       
[59] "  Minimum=-0.714, Maximum=1.000, Mean=0.313, StdDev=0.481"                                                                                                                                                                                                                     
[60] "  NoData Value=-9999"                                                                                                                                                                                                                                                          
[61] "  Metadata:"                                                                                                                                                                                                                                                                   
[62] "    STATISTICS_MAXIMUM=0.99999973665016"                                                                                                                                                                                                                                       
[63] "    STATISTICS_MEAN=0.31255246777216"                                                                                                                                                                                                                                          
[64] "    STATISTICS_MINIMUM=-0.71362979358008"                                                                                                                                                                                                                                      
[65] "    STATISTICS_STDDEV=0.48129385401108"                                                                                                                                                                                                                                        
```


1. If this file has the same CRS as DSM_HARV?  Yes: UTM Zone 18, WGS84, meters.
2. What format `NoDataValues` take?  -9999
3. The resolution of the raster data? 1x1
4. How large a 5x5 pixel area would be? 5mx5m How? We are given resolution of 1x1 and units in meters, therefore resolution of 5x5 means 5x5m.
5. Is the file a multi- or single-band raster?  Single.

:::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::



:::::::::::::::::::::::::::::::::::::::: keypoints

- The GeoTIFF file format includes metadata about the raster data.
- R stores CRS information in the Proj4 format.
- Be careful when dealing with missing or bad data values.

::::::::::::::::::::::::::::::::::::::::::::::::::


