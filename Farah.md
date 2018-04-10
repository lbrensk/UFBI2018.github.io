---
title: "test"
output: html_document
---

---
layout: page
title:  Forest Cover Estimation
permalink: /FarahTest/
---


## Load Maps
***

First, set a folder where you will have shapefiles and images

```{r}
setwd("~/Documents/PhD.Dissertation/Campo2017/Maps&Imagenes/Bosque_No_Bosque_2016_Hib_Raster") 
```


### you will need:
1. intermediate knowledge of R
2. the following packages:

```{r}
library(raster)
library(SDMTools) 
library(maptools) 
library(rgdal) 
```

To upload the raster file

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

# Croping a map
***
Croping the country raster in order to have only the Madre de Dios department

First we generate an object using the UTM coordinates that delimited the study area

```{r}
CorridorMap = extent(1050000, 1190000, 8550000, 8900000) 
```

The function "extent" returns an Extent object of the Raster

We use this object to crop the country raster into a small area

```{r}
cropForest <- crop(Forest, CorridorMap) 
```

We then plot the small raster

```{r}
plot(cropForest)
```

In order to know how many class categories the raster has we use the function "ClassStat".

The function "ClassStat" calculates the class statistics for patch types identified in a raster.

```{r}
SM.Forest.class<-ClassStat(cropForest)
```

In the example there are 4 classes: 1 - non-forest 2000, 2 - forest 2016, 3 - hydrography, 4 - forest loss 2001-2016

# Reclassification
***
We want to reclassify the raster changing 1 (non-forest 2000), 3 (hydrography), 4 (forest loss 2001-2016) = Non Forest 

We create a table with the old and new classes.

```{r}
classification<-matrix(NA, 4,4)
classification[,1]<-c(1,2,3,4)
classification[,2]<-c("NoForest2000","Forest2016","Hidrography","Loss2001-2016")
classification[,3]<-c(1,2,1,1)
classification[,4]<-c("NoForest","Forest","NoForest","NoForest")
colnames(classification)<-c("Landcover","Description","ChangeTo", "Description2")
classification<-as.data.frame(classification) 
str(classification)
classification[,1]<-as.numeric(classification[,1])
classification[,3]<-as.numeric(classification[,3])
class<-classification[,c(1,3)]
str(class)
```

# Reclassify the map

The function "reclassify" (re)classifies groups of values to other values. 

```{r}
rasterFINAL<-reclassify(cropForest,rcl=class)
```

Ploting the reclassified raster

```{r}
plot(rasterFINAL)
```


#Getting the metrics from reclassified raster
SM.rasterFINAL.class<-ClassStat(rasterFINAL) 

```{r}
library(lidR)
library(raster)
pt <-  "./inputs/LiDAR/"
f <- list.files(pt, pattern = ".laz")[1]
las = readLAS(paste(pt, f, sep=""))
```

something

```{r}
image_rgb   = stack(paste(pt, list.files(pt, pattern = "image.tif")[1], sep="/"))
#plotRGB(image_rgb, r=2, g=1, b=3)
# Get each channel in a RasterLayer
ch1 = image_rgb@layers[[1]]
ch2 = image_rgb@layers[[2]]
ch3 = image_rgb@layers[[3]]

# Attribute the channels in fields R,G and B
lasclassify(las, ch1, "R")
lasclassify(las, ch2, "G")
lasclassify(las, ch3, "B")

# build the color palette give the 3 channels
lascolor(las)
```


You can also embed plots, for example:

```{r}
plot(cars)
```

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
