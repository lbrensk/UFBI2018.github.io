---
layout: page
title:  Climate Data
permalink: /Laura/
---

# Download the necessary tools
***
***Please download these BEFORE attending the workshop on May 8. If you have problems, you may contact me via lbrensk@ufl.edu with questions.***

**ClimNA** - You can download the program and necessary files here: [https://sites.ualberta.ca/~ahamann/data/climatena.html](https://sites.ualberta.ca/~ahamann/data/climatena.html)

If you have a Windows machine, you should be able to simple unzip the folder and run the "ClimateNA_v5.21.exe" file.
If you have a Mac, you may require a few other programs to run ClimNA, which is made for Windows. You will need to install [Wine](http://www.winehq.org), which allows you to run Windows apps without installing Windows. I recommend downloading Wine itself from the [winehq website](http://www.winehq.org), but the directions included [here](https://www.davidbaumgold.com/tutorials/wine-mac/#part-1:-install-homebrew) about installing Homebrew and XQuartz, which may be necessary in order to run Wine, are very helpful. Finally, you may need to run the ClimateNA file called “Libraryfiles.exe” in Wine before you are able to run the "ClimateNA_v5.21.exe" file itself.

**Panoply** - You can download this program here: [https://www.giss.nasa.gov/tools/panoply/](https://www.giss.nasa.gov/tools/panoply/)

***

First, set a folder where you will have shapefiles and images

```{r}
setwd("~/Documents/PhD.Dissertation/Campo2017/Maps&Imagenes/Bosque_No_Bosque_2016_Hib_Raster")
```


#### you will need:
1. intermediate knowledge of R
2. the following packages:

```{r}
library(raster)
library(SDMTools)
library(maptools)
library(rgdal)
```

## Uploading raster files

```{r}
Forest <- raster("Bosque_No_bosque_2016.tif")
```

To know the projection of the raster

```{r}
projection(Forest)
```

To know the resolution of the raster

```{r}
res(Forest)
```

To plot the raster, in this case this is for whole country

```{r}
plot(Forest)
```
