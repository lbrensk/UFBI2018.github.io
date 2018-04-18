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

You will need to download the data file called "dasypus_novemcinctus_gbif.csv". This is a dataset of hummingbird clearwing moth (*Hemaris thysbe*) occurrences that I downloaded directly from GBIF's website and converted into a .csv file. We will clean it to prepare it for ClimNA using R. First, set your working directory if you have not already and load the required libraries. You will also need to install the R package dismo.

```{r}
setwd("~/Desktop/UFBI workshop example/");
library(scrubr);
library(readr);
#install.packages("dismo");
library(dismo);
```

Load the dataset you downloaded into R.

```{r}
library(readr)
hemaris_gbif <- read_csv("~/Desktop/UFBI workshop example/Armadillo/dasypus-novemcinctus-gbif.csv")
View(hemaris_gbif)
```

This dataset contains a lot more data than we need for running ClimNA. Let's first transform this into a data frame and extract only the necessary columns necessary: occurrenceID, publishing organization code, latitude, longitude, elevation, and event date.

```{r}
data <- data.frame(hemaris_gbif)
hemaris <- data[,c(3,16,17,18,21,25)]
View(hemaris)
```

![Image of Screen after this step](https://lh3.googleusercontent.com/mnUnNg5fV_C6jMnoCmUArq1TlP1DBFIx1eU_kiX3eTuykylqSkwNSgbLtAiOR8P29Nc-vQTDt8-nTr56wZrAq7EW0u9V9ypNgS6x5r3-OpYXutYbJnrdCzWoziT9F7kuFP8bnL80bQ44Ooy_JHQ7wmWJyZiJtYPCyBbRJcMCGbjjlwZyYxxEGZ5jSxO3Em2dOJH6wQukjmxK6mx47nh9g52PGZMzw0vjKfGjmukM_vKXaa8svfPj7S_QFI10zw1W61bx1XVAJ4gW7kFW3Znl_Y9XAC1QQ0EzQ51Hb7SYh44oeMWKKSKE9CLGiJhVwq1cMHUQWRxPw1uSlgKse9WuKZOzGq3vY7XVRN1cocEn7uz7GAwrkF3Ns9b-w0JgefNrMYsrhC8t6D9rrI0prhwmZPM2WC-6iHGYID62foMG-2O9th14b1pTzAHUQ--6TGY7_6dJo6q1mbPAeQc9OMrQdb-1b_xdymI79ACPrcn73MaQGN_jBruVWPgJFtWhSaNbktazRYLibqnMPlN08QkpGnmMmWzEN5dtTLn0IO-Gmcetgh6CqflRXUkMztXx4QnWMrFcn5ZJ_HZ6ComTwAHxGRV2X5pOIocR0rfNaOyEsr3U3LD7yZ9WrwtlwHEo1U21LCityKLqGkyAVfWOZ8PCjWDtCSUc-Ng7=w1141-h471-no)

Now we are going to clean up our 1421 records to narrow it down further. We only want occurrence records that have data in all of the fields we've specified in our search.

```{r}
cleaned_occ <- (as.data.frame(hemaris)[complete.cases(as.data.frame(hemaris)), ])
```

Next, let's clean these data to get rid of incomplete coordinates, unlikely coordinates, and duplicate records.

```{r}
scrubbed <- coord_incomplete(cleaned_occ)
scrubbed_data <- coord_unlikely(scrubbed)
unique_data <- unique(scrubbed_data)
View(unique_data)
```
You'll notice even after these simple cleaning steps, we have gone from 1421 records to only 16, but for our purposes here, this is ideal.

Next, we will have to do some reformatting because ClimNA is particular about the formatting of the .csv you give it.

```{r}
names(unique_data) <- c("ID1", "ID2", "lat", "long", "el", "date")
View(unique_data)
```
ClimNA is very picky about formatting. Above, we renamed our columns to suit the required formatting:<br>
occurrenceID -> ID1<br>
publishingorgkey -> ID2<br>
decimallatitude -> lat<br>
decimallongitude -> long<br>
elevation -> el<br>

ClimNA itself does not care about or want the eventDate -> date information. However, we will need it for processing post-ClimNA so we will save a copy of these data with the date column intact, then delete this column and save the dataset to be put into ClimNA.

```{r}
write.csv(unique_data, "Hemaris-clean-dates.csv", row.names = F)
unique_data$date <- NULL
View(unique_data)
write.csv(unique_data, "Hemaris-ClimNA.csv", row.names = F)
```
