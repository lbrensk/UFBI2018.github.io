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

## Climate data resources overview

***

## ClimNA example

You will need to download the data file called "dasypus_novemcinctus_gbif.csv". This is a dataset of nine-banded armadillo (*Dasypus novemcinctus*) occurrences that I downloaded directly from GBIF's website and converted into a .csv file. We will clean it to prepare it for ClimNA using R. First, set your working directory if you have not already and load the required libraries.

```{r}
setwd("~/Desktop/UFBI workshop example/");
library(scrubr);
library(readr);
```

Load the dataset you downloaded into R.

```{r}
library(readr)
dasypus_novemcinctus_gbif <- read_csv("~/Desktop/UFBI workshop example/Armadillo/dasypus-novemcinctus-gbif.csv")
View(dasypus_novemcinctus_gbif)
```

This dataset contains a lot more data than we need for running ClimNA. Let's first transform this into a data frame and extract only the columns necessary for the ClimNA program to run.

```{r}
data <- data.frame(dasypus_novemcinctus_gbif)
dasypus <- data[,c(3,16,17,18,21)]
View(dasypus)
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

Next, we will have to do some reformatting because ClimNA is particular about the formatting of the .csv you give it.
