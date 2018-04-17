---
layout: page
title:  Climate Data
permalink: /Laura/
---

# Download the necessary tools
***Please download these BEFORE attending the workshop on May 8. If you have problems, you may contact me via lbrensk@ufl.edu with questions.***

**ClimNA** - You can download the program and necessary files here: [https://sites.ualberta.ca/~ahamann/data/climatena.html](https://sites.ualberta.ca/~ahamann/data/climatena.html)

If you have a Windows machine, you should be able to simple unzip the folder and run the "ClimateNA_v5.21.exe" file.
If you have a Mac, you may require a few other programs to run ClimNA, which is made for Windows. You will need to install [Wine](http://www.winehq.org), which allows you to run Windows apps without installing Windows. I recommend downloading Wine itself from the [winehq website](http://www.winehq.org), but the directions included [here](https://www.davidbaumgold.com/tutorials/wine-mac/#part-1:-install-homebrew) about installing Homebrew and XQuartz, which may be necessary in order to run Wine, are very helpful. Finally, you may need to run the ClimateNA file called “Libraryfiles.exe” in Wine before you are able to run the "ClimateNA_v5.21.exe" file itself.

**Panoply** - You can download this program here: [https://www.giss.nasa.gov/tools/panoply/](https://www.giss.nasa.gov/tools/panoply/)

***

## ClimNA example

First, set your working directory if you have not already.

```{r}
setwd("~/Desktop/UFBI workshop example/");
```

You will need these libraries. First, we will practice pulling and cleaning occurrence data, but for this example, we will use GBIF. 

```{r}
library(rgbif);
library(scrubr);
```
Let's search for armadillo (*Dasypus novemcinctus*) occurrences! Specifically, we need occurrences that have latitude, longitude, and elevation data. You'll notice in the code that we are limiting our search to 1000 records; this is only of demonstration purposes. I do not want ClimNA to be bogged down creating data records for thousands of occurrences while we wait in the workshop.

```{r}
species <- "Dasypus novemcinctus"
> occ <- occ_search(scientificName = "Dasypus novemcinctus", hasCoordinate = TRUE, fields = c("name", "key", "decimalLatitude", "decimalLongitude", "elevation"), limit = 1000, return = 'data')
```

Now we are going to clean up our 1000 records to narrow it down further. We only want occurrence records that have data in all of the fields we've specified in our search.

```{r}
cleaned_occ <- (as.data.frame(occ)[complete.cases(as.data.frame(occ)), ])
```

Next, let's clean these data to get rid of incomplete coordinates, unlikely coordinates, and duplicate records.

```{r}
scrubbed <- coord_incomplete(cleaned_occ)
scrubbed_data <- coord_unlikely(scrubbed)
unique_data <- unique(scrubbed_data)
view(unique_data);
```
You'll notice even after these simple cleaning steps, we have gone from 1000 records to only 121, but for our purposes here, this is fine.
