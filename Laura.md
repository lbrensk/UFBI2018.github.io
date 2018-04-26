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

We will be using ClimNA as our example for this workshop, which ***only works for occurrences in North America***. Be aware that there are other resources available for **South America** and **Europe**: [https://sites.ualberta.ca/~ahamann/data.html](https://sites.ualberta.ca/~ahamann/data.html).<br>These should have very similar functionalities to what we're learning in the workshop.

**WorldClim** - [http://worldclim.org/](http://worldclim.org/)

***

## ClimNA example

You will need to download the data file called **dasypus_novemcinctus_gbif.csv**. This is a dataset of hummingbird clearwing moth (*Hemaris thysbe*) occurrences that I downloaded directly from GBIF's website and converted into a .csv file. We will clean it to prepare it for ClimNA using R. First, set your working directory if you have not already and load the required libraries. You will also need to install the R package dismo.

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
### Cleaning data for ClimNA

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

The two ID fields are important if you want to go back to the larger spreadsheet that we cleaned to view or verify the rest of the associated information with the specimens you are using in your analysis.

ClimNA itself does not care about or want the eventDate -> date information. However, we will need it for processing post-ClimNA so we will save a copy of these data with the date column intact, then delete this column and save the dataset to be put into ClimNA.

```{r}
write.csv(unique_data, "Hemaris-clean-dates.csv", row.names = F)
unique_data$date <- NULL
View(unique_data)
write.csv(unique_data, "Hemaris-ClimNA.csv", row.names = F)
```
### Running ClimNA

Now we are actually ready to run the ClimNA program with the data we've cleaned. Open the **ClimateNA_v5.21.exe** file on your computer. If you have a Mac and have not installed Wine or the other necessary programs to run this Windows program, see at the top of this page because you will experience difficulties at this step. **This is why it is important to do this BEFORE coming to the workshop!** Otherwise, we will waste time troubleshooting Wine installation.

At this step, it is important to open the **Hemaris-clean-dates.csv** file to check when our occurrences come from. For our particular example, the occurrences range from 1969 to 2017. Unfortunately, ClimNA only has data up until 2013. For simplicity's sake, it is probably best to delete the two occurrences that fall outside of ClimNA's range (shown highlighted in the image below).

<img src="https://lh3.googleusercontent.com/ffl5mW_x-L399HY3xu70OPznF_WGLqbnoCUflALsn0Ndxvt42_J_Fw5dnaku0Mg4BqG1N4mrJAqxQC9qPpjFhjGJqSoP3qcDebbh72L5ye3ed40AiX9r8TiZhzMtL3B_l1MFNp6IxVMeVEwJzeywCZYUhVFhgaM12ika89zGrLZLqKJ1pOW4btexYrFFIY_Llmqu9uBaVsoFgofgjPpdTXpjtZhp_L4PCk-BGlgP26gq1pcyPNk4Hj92hATpE3XYVk4JvUqK9Il6h9hjULrma3A4JyAwkzu1J2HYIYqLJYXzaxVv5Qg9Kp3RNLhGYFCV-VoUr1RcMqwP7vkfd_AsfIbVgsXkdMU-ukr8JYz5tkjBX3x9c1KujY0kkk-k8awKxZMHFnqIzMv3J2VQ_8f-3NEL0Dr0AtkbLF3sTuNANEZMRQyn7hWvR7AsFTKQhUvonhnuFM18TcevB7Yyfj9hKahbPjE68JcNQ4QuOPvYFI2O48SXJD8G-Xxw1Pfz0POU3A0yqMWsPD3gQyOL7ll6Ay-fZAVobcwdyV4tKmvrhUyXOXgTumjRub-joR3YLIuWWatJTgILINlYzHZcUIv0oFJn08rUYnM9Dpp1oRM=w934-h592-no" width="550" height="330"/>

Note the ID1 and ID2 for these records, and delete them from the **Hemaris-ClimNA.csv** file and save it. You will need to save a blank spreadsheet as a .csv file for your output from ClimNA. Fill out the ClimNA interface with the appropriate input, output, and year range. We will discuss more about these options in the workshop. Click "Calculate TS".

<img src="https://lh3.googleusercontent.com/pd2aFelNni0YcOglFZjjk2OYLCg2wofOeZ3zgcnahJkn5PdjBGxdMWLyiGZOv9IhDP0VjOsKaS2SqJwaa5ph3pDn_8pwLvwJSnpSlDZlriGwOEBTZ-cwiDIz5VQiJHDnIc3KLUWL8p6S-nENHgRMp0HuTQUrJDSVdK6ofUtpqd44cMkGmupPi331vaAZs_HQ5ewBnbIidy5VxuF3ScZ6dojSeZ2kH4dgtAJfP1OLdXS9v51rIKoCBd9lPxOWlPLToKPIUvPenpijxskXk2rHzmBh885947L3A_9Mk9wsECH5FrCX7oT3WZLHt9Gx5MZXdU0-_syG4lwQdxHtUltbbrfKQgcKzoHii9bYTkXIjHX0r-Zf6-gaGlXoqkUShpPpCY7s9Zsgw9AJ-Kucm7yD2oek9O5kXWzJ9Z2FkA-gsAoDks4_k0vbxBCQ9WRWDtWf_8QZYegK-gi47vr-tPZM0yFXsgu15HK-gfqbNwI3K5-LevyIGmIINQLog92_ZYpUu4MhLhpxQqN3FbCUCpFKv6S5pY_FJf8KuijoWX3jaI-VSECPMcVQp1YaQI_8ul6MLByFzu_CbIB7zCTtOA-rPfoYakuxqvy6XZhBwls=w1122-h1134-no" width="430" height="450"/>

If you open your output file, it should now have data in it. Because we ran these as a time series, you'll notice that all of our occurrences now have a row of data with every annual variable from ClimNA for every year from 1969-2013. The definitions of these annual climate variables can be found in the **help.rtf** ClimNA file.

<img src="https://lh3.googleusercontent.com/H5fmi2X0CtQc8ULnfICRVaDf06kIJy91a2AAOSWFb1-rPupBJBfSoBxOHImFbAEPqiytxzTNt-R1YFCvgfYaaKq5oa76pvYFnJ_9uuSTXtkEIm89T1KZhx8igBi4BvqtOU7LW8GCA0UOYI-wJXapc_WPCz7EPA4CaL_AKXz68pwU6PL-ywD61BXlrflx25ssbYqf5vShUbq9dSXWRsqBO0Oc4TQ2aF_QBx4t9mp9A3rb4sGAnnQ1suaV20X3i4hfrXNv2G45pSE1s_3AcX8Nh_foHo4m7SOqc1gqe7CCSa9b_CgRkLc_bZXRR5iXkbquvEq33e_YpNkxa1myuSdu1jsuqcOERYr_KW4a7BwbLGLpFbBiIWRfUqjMsnb-l0YF_Al7W66_zGi-DBXbXRkMz7jk0x6c4HX70QYQdWpv-Eu8h3OWZQob450X22w87l-kVONNqKqj1vMZSmwI0Q1zNe0OgG0XGFWDStXyfMziN2wD1q0YaMQhikIVChDXeuJcn73Oel7BwJW9LT9FjrruRzTffN0FKY_OOHUpjTsYxAVtPDeXB71d13TUMLe1YuH5IMkBbGNCyOjAzoV63gZAMTRWvGKubEWtR3mbAfU=w900-h994-no" width="500" height="530"/>

### Cleaning the ClimNA output

Really, we only need the associated climate data from the year that the occurrence was recorded. In order to keep this example simple, we will only pull the MAT (mean annual temperature) and MAP (mean annual precipitation) for the year we need, and then we will use the biovars function to create climate variables from the data we have.
