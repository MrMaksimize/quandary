---
layout: post
title: GIS and Spatial Joins in Tableau
image: http://mrm-screen.s3.amazonaws.com/tableau_gas_stations-1.png
author:
  name: Maksim Pecherskiy
  twitter: mrmaksimize
---

We own several Tableau licenses at the City of San Diego. We use it for a few things, the most visible of which is [PerformSD](http://performance.sandiego.gov), which our Open Data Coordinator [Andrell](https://www.linkedin.com/in/andrell-bower-2b576a25) absolutely rocked.  

I've been playing around a bit with Tableau and wanted to see which Community Planning Districts have the highest density of gas stations (yes, I do ask questions like that). Tableau is not meant for heavy GIS operations, but I could see myself wanting to build a dashboard around a similar use-case.

## What we have
* [A Shapefile of Community Planning Areas from the City of SD Data Portal](http://data.sandiego.gov/dataset/community-planning-district-boundaries).
* A [Shapefile of gas stations from SanGIS](http://mrm-screen.s3.amazonaws.com/Gas_Stations.zip)

## Data We Need To Give Tableau
* A CSV file of points that outline the borders of each of the polygons
* A CSV file of gas stations with a column that references the ID of the Community Planning District.

## Convert Community Planning Districts to CSV
Tableau is not capable of working with Shapefiles at all (although that's slated to change in Tableau 10). This means that the first thing we need to do is convert the Community Planning Districts (CPD) Shapefile to a CSV of points that outline the borders of each of the polygon.

Luckily, this task already has several solutions. The easiest way is to do it using this hosted [alteryx job]([alteryx job](https://gallery.alteryx.com/#!app/Tableau-Shapefile-to-Polygon-Converter/5296f89120aaf905b8e7fb48)).

You can also use this [R Script](https://github.com/msolbrig/TabShapeR) to get the same job done.

Or, just pick up [the ones I have already converted](http://mrm-screen.s3.amazonaws.com/ComPlan.csv) (and don't forget the [metadata](http://mrm-screen.s3.amazonaws.com/Community_Plan_SD%20(1).pdf)).

Now, put that file somewhere safe, and let's go back to the community planning ***Shapefile*** for the next section.

Now we need a **A CSV file of gas stations, with a column that references the ID of the Community Planning District**.
To get this, we need to perform a spatial join between the CPD and the gas stations Shapefile.

You can perform this operation in any GIS tool (ArcGIS, CartoDB, Python, etc). I've been digging R a lot lately, so that's what we'll use here. A spatial join is a geospatial operation to figure out which geographical objects are located within other geographical objects. In our case, we're going to figure out which requests (points) belong in which polygons (CPD).

Let's do some setup We're going to use the original CPD Shapefile we got from SANGIS, and the shapefile with all the gas stations:

```
directory <- "."

library(rgdal) #conatins the read/writeOGR for reading shapelies and read/writeRGDAL for reading raster data
library(rgeos) # neccessary for ggplot2::fortify.sp(); serves as a replacement for gpclib
library(maptools) #Contains the overlay command
library(dplyr) # For data magic

setwd(directory)

# Lat / Lon projection string.
latlon <- "+init=epsg:4326"

gasStations <- readOGR("./data/Gas_Stations", 
                       layer = "GAS_STATIONS")

com_plan <- readOGR("./data/Community_Plan_SD", 
                    layer = "Community_Plan_SD")

```

Now, let's execute the spatial join in R

```
gasStationsCPD <- over(gasStations, com_plan)
# Since it's really based by rownumber, let's do a safe join of the new 
# gas station data, and the gas stations belonging in each community planning
# district.
gasStations@data <- as.data.frame(bind_cols(gasStations@data, gasStationsCPD))

# How many match?
nMatches <- nrow(gasStationsCPD[complete.cases(gasStationsCPD), ])
```

This will give us a dataframe with 787 rows, or the number of rows in our gas stations Shapefile. The blank rows simply don't match a community planning district. 291 rows are not blank, meaning 291 out of 787 gas stations are located in a community planning district in San Diego. Since our goal here is to do as much data manipulation in Tableau as possible to allow for maximum end-user flexibility, we will leave the task of filtering out the blank rows to Tableau.

We still need to give Tableau a CSV with lat/longs, and currently gasStationsCPD is a SpatialPointsDataFrame. Let's make a CSV.

```
# First, let's adjust the coordinates to lat/long
gsDF <- spTransform(gasStations, CRS(latlon))

gsDF <- as.data.frame(gsDF) %>%
    rename(latitude = coords.x1, longitude = coords.x2)

write.csv(gsDF, file = "./data/tableau_out/gas_stations.csv")
```

This will give us our gas station file with a column referencing a Community Planning District, and that column will be populated if the gas station is located within one.

## Putting it all together
Now, we have our CSV file with a set of points that outline the edges of the Community Planning District polygons and our CSV file with Gas Stations, their lat/longs, and a reference to which community district within which they are located.
To start building in Tableau, we need to add our data sources first. Make sure to put ComPlan.csv and gas_stations.csv in the same folder. For some reason, Tableau likes it better that way.

Then, drag in the ComPlan.csv file, followed by the gas stations csv:

![Tableau1](http://take.ms/koYwj)

Tableau will pop up the fields for you to join the data. Select the match on CPNAME, and now they're joined.  

Next, let's do some mapping!

1. Command- (or control) click on Lattitude and Longitude measures under Com Plan
2. Click the map under Show Me
3. Drag CPNAME dimension from ComPlan under marks
4. Drag Polygon ID dimension from ComPlan under marks
5. Change Mark Type to Polygon
6. Drag Point ID dimension from ComPlan under marks
7. Drag Number of Records measure from Gas Stations to the color box
8. Drag CPNAME dimension from ComPlan under marks.

We have a map of community planning districts colored by the density of gas stations in each!

We can also add a filter:

![Tableau2](http://take.ms/Up4JJ)

This lets us filter by the number of gas stations in a Community Planning District.


## What's Next?

The gas_stations.csv file has plenty of interesting attributes, so it's time to let your imagination run wild.  By adding that simple column of community planning district id to the CSV file of gas stations, we have given ourselves to do all kinds of filtering by the community planning district.

You can pick up the relevant code, Tableau workbook and links to files in the [Github Repo](https://github.com/MrMaksimize/RTableauGasStationsExperiment)

If you find something wrong or disagree with me, you know what to do :)   



