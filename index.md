---
title: "Summary and Set-up"
author: Mariana Castaneda-Guzman
site: sandpaper::sandpaper_site
---

<p></p>

<div style="text-align: center; margin-top: 30px; margin-bottom: 30px;">

![](episodes/fig/Vertical_VT_Full_Color_RGB.jpg){alt="" style="width: 25%; display: inline-block;"}
![](episodes/fig/CDC_logo.jpg){alt="" style="width: 25%; display: inline-block;"}
![](episodes/fig/NCDC_logo.png){alt="" style="width: 25%; display: inline-block;"}
![](episodes/fig/DTRA_logo.png){alt="" style="width: 25%; display: inline-block;"}

</div>

<p></p>

The episodes in this lesson cover how to open, work with, and plot
vector and raster-format spatial data in R. Additional topics include
working with spatial metadata (extent and coordinate reference systems),
reprojecting spatial data, and working with raster time series data.

This lesson assumes you have some knowledge of `R.` If you've never
used `R` before or need a refresher, start with our
[Introduction to R](https://castanedam.github.io/DTRA_workshop_R/)
lesson.

::::::::::::::::::::::::::::::::::::::::::  prereq

### Download Data

The data and lessons in this workshop were originally developed through a hackathon funded by the
[National Ecological Observatory Network (NEON)](https://www.neonscience.org/) - an NSF funded observatory in Boulder, Colorado - in
collaboration with Data Carpentry, SESYNC and CYVERSE. NEON is collecting data for 30 years to help scientists understand
how aquatic and terrestrial ecosystems are changing. The data used in these lessons cover two NEON field sites:

- Harvard Forest (HARV) - Massachusetts, USA - [fieldsite description](https://www.neonscience.org/field-sites/field-sites-map/HARV)
- San Joaquin Experimental Range (SJER) - California, USA - [fieldsite description](https://www.neonscience.org/field-sites/field-sites-map/SJER)

There are four data sets included, all of which are available
[on Figshare](https://figshare.com/articles/Spatio_temporal_Series_Teaching_Data_Subsets/2009586)
under a CC-BY license. You can download all of the data used in this workshop by clicking
[this download link](https://ndownloader.figshare.com/articles/2009586/versions/10).
Clicking the download link will download all of the files as a single compressed
(`.zip`) file. To expand this file, double-click the folder icon in your file navigator application (for Macs, this is the Finder
application).

These data files represent the teaching version of the data, with sufficient complexity to teach many aspects of  data analysis and
management, but with many complexities removed to allow students to focus on the core ideas and skills being taught.

| Dataset                      | File name                                                                                  | Description                                                                                                                                                                                                                                             | 
| ------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Site layout shapefiles       | NEON-DS-Site-Layout-Files.zip                                                              | A set of shapefiles for the NEON's Harvard Forest field site and US and (some) state boundary layers.                                                                                                                                                   | 
| Meteorological data          | NEON-DS-Met-Time-Series.zip                                                                | Precipitation, temperature and other variables collected from a flux tower at the NEON Harvard Forest site                                                                                                                                              | 
| Airborne remote sensing data | NEON-DS-Airborne-RemoteSensing.zip                                                         | LiDAR data collected by the NEON Airborne Observation Platform (AOP) and processed at NEON including a canopy height model, digital elevation model and digital surface model for NEON's Harvard Forest and San Joaquin Experimental Range field sites. | 
| Landstat 7 NDVI raster data  | NEON-DS-Landsat-NDVI.zip                                                                   | 2011 NDVI data product derived from Landsat 7 and processed by USGS cropped to NEON's Harvard Forest and San Joaquin Experimental Range field sites                                                                                                     | 

[More information on this dataset](instructors/data.md)

::::::::::::::::::::::::::::::::::::::::::::::::::


