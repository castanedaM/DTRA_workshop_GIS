---
title: Intro to Raster Data
teaching: 30
exercises: 20
source: Rmd
---



::::::::::::::::::::::::::::::::::::::: objectives

- Describe the fundamental attributes of a raster dataset.
- Explore raster attributes and metadata using R.
- Import rasters into R using the `terra` package.
- Plot a raster file in R using the `ggplot2` package.
- Describe the difference between single- and multi-band rasters.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- What is a raster dataset?
- How do I work with and plot raster data in R?
- How can I handle missing or bad data values for a raster?

::::::::::::::::::::::::::::::::::::::::::::::::::



::::::::::::::::::::::::::::::::::::::::::  prereq

## Things You'll Need To Complete This Episode

See the [lesson homepage](.) for detailed information about the software,
data, and other prerequisites you will need to work through the examples in this episode.


::::::::::::::::::::::::::::::::::::::::::::::::::

In this episode, we will introduce the fundamental principles, packages and
metadata/raster attributes that are needed to work with raster data in R. We will
discuss some of the core metadata elements that we need to understand to work with
rasters in R, including CRS and resolution. We will also explore missing and bad
data values as stored in a raster and how R handles these elements.

We will continue to work with the `dplyr` and `ggplot2` packages that were introduced
in the [Introduction to R](https://castanedam.github.io/DTRA_workshop_R/) 
lesson. We will use two additional packages in this episode to work with raster 
data - the `terra` and `sf` packages. Make sure that you have these packages 
loaded.


```r
library(terra)
library(ggplot2)
library(dplyr)
```

:::::::::::::::::::::::::::::::::::::::::  callout

## Introduce the Data

If not already discussed, introduce the datasets that will be used in this
lesson. A brief introduction to the datasets can be found on the
[Geospatial workshop homepage](https://datacarpentry.org/geospatial-workshop/#data).

For more detailed information about the datasets, check
out the [Geospatial workshop data
page](https://datacarpentry.org/geospatial-workshop/data/).


::::::::::::::::::::::::::::::::::::::::::::::::::

## Open a Raster in R

Now that we've previewed the metadata for our GeoTIFF, let's import this
raster dataset into R and explore its metadata more closely. We can use the `rast()`
function to open a raster in R.

:::::::::::::::::::::::::::::::::::::::::  callout

## Data Tip - Object names

To improve code
readability, file and object names should be used that make it clear what is in
the file. The data for this episode were collected from Harvard Forest so
we'll use a naming convention of `datatype_HARV`.


::::::::::::::::::::::::::::::::::::::::::::::::::

First we will load our raster file into R and view the data structure.


```r
DSM_HARV <-
  rast("data/NEON-DS-Airborne-Remote-Sensing/HARV/DSM/HARV_dsmCrop.tif")

DSM_HARV
```

```{.output}
class       : SpatRaster 
dimensions  : 1367, 1697, 1  (nrow, ncol, nlyr)
resolution  : 1, 1  (x, y)
extent      : 731453, 733150, 4712471, 4713838  (xmin, xmax, ymin, ymax)
coord. ref. : WGS 84 / UTM zone 18N (EPSG:32618) 
source      : HARV_dsmCrop.tif 
name        : HARV_dsmCrop 
min value   :       305.07 
max value   :       416.07 
```

The information above includes a report of min and max values, but no other data
range statistics. Similar to other R data structures like vectors and data frame
columns, descriptive statistics for raster data can be retrieved like


```r
summary(DSM_HARV)
```

```{.warning}
Warning: [summary] used a sample
```

```{.output}
  HARV_dsmCrop  
 Min.   :305.6  
 1st Qu.:345.6  
 Median :359.6  
 Mean   :359.8  
 3rd Qu.:374.3  
 Max.   :414.7  
```

but note the warning - unless you force R to calculate these statistics using
every cell in the raster, it will take a random sample of 100,000 cells and
calculate from that instead. To force calculation all the values, you can use 
the function `values`:


```r
summary(values(DSM_HARV))
```

```{.output}
  HARV_dsmCrop  
 Min.   :305.1  
 1st Qu.:345.6  
 Median :359.7  
 Mean   :359.9  
 3rd Qu.:374.3  
 Max.   :416.1  
```

To visualise this data in R using `ggplot2`, we need to convert it to a
dataframe. We learned about dataframes in [an earlier
lesson](https://datacarpentry.org/r-intro-geospatial/04-data-structures-part2/index.html).
The `terra` package has an built-in function for conversion to a plotable dataframe.


```r
DSM_HARV_df <- as.data.frame(DSM_HARV, xy = TRUE)
```

Now when we view the structure of our data, we will see a standard
dataframe format.


```r
str(DSM_HARV_df)
```

```{.output}
'data.frame':	2319799 obs. of  3 variables:
 $ x           : num  731454 731454 731456 731456 731458 ...
 $ y           : num  4713838 4713838 4713838 4713838 4713838 ...
 $ HARV_dsmCrop: num  409 408 407 407 409 ...
```

We can use `ggplot()` to plot this data. We will set the color scale to 
`scale_fill_viridis_c` which is a color-blindness friendly color scale. We will 
also use the `coord_quickmap()` function to use an approximate Mercator 
projection for our plots. This approximation is suitable for small areas that 
are not too close to the poles. Other coordinate systems are available in 
ggplot2 if needed, you can learn about them at their help page `?coord_map`.


```r
ggplot() +
    geom_raster(data = DSM_HARV_df , aes(x = x, y = y, fill = HARV_dsmCrop)) +
    scale_fill_viridis_c() +
    coord_quickmap()
```

<div class="figure" style="text-align: center">
<img src="fig/01-raster-structure-rendered-ggplot-raster-1.png" alt="Raster plot with ggplot2 using the viridis color scale"  />
<p class="caption">Raster plot with ggplot2 using the viridis color scale</p>
</div>

::::::::::::::::  callout

## Plotting Tip

More information about the Viridis palette used above at
[R Viridis package documentation](https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html).

::::::::::::::::::


:::::::::::::::::::::::::::::::::::::::::  challenge

## Plotting Tip

For faster, simpler plots, you can use the `plot` function from the `terra` package.


:::::::::::::::  solution

## Show plot

See `?plot` for more arguments to customize the plot


```r
plot(DSM_HARV)
```

<img src="fig/01-raster-structure-rendered-unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

:::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::


This map shows the elevation of our study site in Harvard Forest. From the
legend, we can see that the maximum elevation is ~400, but we can't tell whether
this is 400 feet or 400 meters because the legend doesn't show us the units. We
can look at the metadata of our object to see what the units are. Much of the
metadata that we're interested in is part of the CRS. We introduced the
concept of a CRS in [an earlier
lesson](https://datacarpentry.org/organization-geospatial/03-crs).

Now we will see how features of the CRS appear in our data file and what
meanings they have.

### View Raster Coordinate Reference System (CRS) in R

We can view the CRS string associated with our R object using the`crs()`
function.


```r
crs(DSM_HARV, proj = TRUE)
```

```{.output}
[1] "+proj=utm +zone=18 +datum=WGS84 +units=m +no_defs"
```

:::::::::::::::::::::::::::::::::::::::  challenge

## Challenge

What units are our data in?

:::::::::::::::  solution

## Answers

`+units=m` tells us that our data is in meters.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Understanding CRS in Proj4 Format

The CRS for our data is given to us by R in `proj4` format. Let's break down
the pieces of `proj4` string. The string contains all of the individual CRS
elements that R or another GIS might need. Each element is specified with a
`+` sign, similar to how a `.csv` file is delimited or broken up by a `,`. After
each `+` we see the CRS element being defined. For example projection (`proj=`)
and datum (`datum=`).

### UTM Proj4 String

A projection string (like the one of `DSM_HARV`) specifies the UTM projection 
as follows:

`+proj=utm +zone=18 +datum=WGS84 +units=m +no_defs +ellps=WGS84 +towgs84=0,0,0`

- **proj=utm:** the projection is UTM, UTM has several zones.
- **zone=18:** the zone is 18
- **datum=WGS84:** the datum is WGS84 (the datum refers to the  0,0 reference for
  the coordinate system used in the projection)
- **units=m:** the units for the coordinates are in meters
- **ellps=WGS84:** the ellipsoid (how the earth's  roundness is calculated) for
  the data is WGS84

Note that the zone is unique to the UTM projection. Not all CRSs will have a
zone. Image source: Chrismurf at English Wikipedia, via [Wikimedia Commons](https://en.wikipedia.org/wiki/Universal_Transverse_Mercator_coordinate_system#/media/File:Utm-zones-USA.svg) (CC-BY).


![The UTM zones across the continental United States. From: https://upload.wikimedia.org/wikipedia/commons/8/8d/Utm-zones-USA.svg](fig/Utm-zones-USA.svg){alt='UTM zones in the USA.'}

## Calculate Raster Min and Max Values

It is useful to know the minimum or maximum values of a raster dataset. In this
case, given we are working with elevation data, these values represent the
min/max elevation range at our site.

Raster statistics are often calculated and embedded in a GeoTIFF for us. We
can view these values:


```r
minmax(DSM_HARV)
```

```{.output}
    HARV_dsmCrop
min       305.07
max       416.07
```

```r
min(values(DSM_HARV))
```

```{.output}
[1] 305.07
```

```r
max(values(DSM_HARV))
```

```{.output}
[1] 416.07
```

:::::::::::::::::::::::::::::::::::::::::  callout

## Data Tip - Set min and max values

If the minimum and maximum values haven't already been
calculated, we can calculate them using the
`setMinMax()` function.


```r
DSM_HARV <- setMinMax(DSM_HARV)
```

::::::::::::::::::::::::::::::::::::::::::::::::::

We can see that the elevation at our site ranges from 305.0700073m to
416.0699768m.


:::::::::::::::::::::::::::::::::::::::::  callout

## More Resources

- [Read more about the `terra` package in R.](https://cran.r-project.org/package=terra)
  

::::::::::::::::::::::::::::::::::::::::::::::::::



:::::::::::::::::::::::::::::::::::::::: keypoints

- The GeoTIFF file format includes metadata about the raster data.
- To plot raster data with the `ggplot2` package, we need to convert it to a dataframe.
- R stores CRS information in the Proj4 format.
- Be careful when dealing with missing or bad data values.

::::::::::::::::::::::::::::::::::::::::::::::::::


