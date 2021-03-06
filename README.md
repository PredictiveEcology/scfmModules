---
title: "integratedSask"
author: ""
date: "21 September 2018"
output: pdf_document
---

# Overview

This is a parent module to run a toy example that integrates models of forest succession, fire, harvesting, birds and caribou populations in  Saskatchewan.

# Usage

```{r module_usage}
# Load libraries  
  library(SpaDES)
  library(magrittr)
  library(raster)

# Set the directories
  workDirectory <- getwd()
  
  paths <- list(
    cachePath = file.path(workDirectory, "cache"),
    modulePath = file.path(workDirectory, "modules"),
    inputPath = file.path(workDirectory, "inputs"),
    outputPath = file.path(workDirectory, "outputs")
  )
  
  setPaths(modulePath = paths$modulePath, inputPath = paths$inputPath, outputPath = paths$outputPath, cachePath = paths$cachePath)

# List the parameters to use
  times <- list(start = 0, end = 15)
  parameters <- list(
  Hanzlik = list(
  replanInterval = 10,
  rationPeriodMultiplier = 2
  ),
  stateVars = list(persistTimes = c(20, 10, 10)),
  ageModule  = list(initialAge = 25,
  .plotInitialTime = 0),
  scfmSpread = list(.plotInitialTime = 0,
  pOverRide = 0.28)
  )
  
modules <- list("integratedSask")

objects <- list(scfmPars = list(pSpread=0.225,
                    p0 = 0.115,
                    naiveP0 = 0.15, 
                    pIgnition = 0.04, #0.00004, #0.0000112,
                    maxBurnCells=NA
                    ),
                  numTypes = 8,
                  landscapeAttr = list(cellSize=6.25),
                  nNbrs = 8,
                  url.vegMap = "ftp://ftp.ccrs.nrcan.gc.ca/ad/NLCCLandCover/LandcoverCanada2005_250m/LandCoverOfCanada2005_V1_4.zip",
                  url.studyArea = "http://sis.agr.gc.ca/cansis/nsdb/ecostrat/district/ecodistrict_shp.zip",
                  areaInHa = pi,
                   polyMatrix = matrix(c(-113.552320, 55.442529), ncol = 2), areaSize = 50000
              )
 
mySim <- simInit(times = times, params = parameters, modules = modules,
                 objects = objects)

mySimOut <- spades(mySim)
```

# Events

This modules prepares the area to be cropped to, initializes a vegetation map, spreads wildfires, harvest in areas where the fire did not spread and where there is a minimum amount of forest, calculates the probability of finding two species of boreal birds in the area, and simulates a caribou population in the area.

## Plotting

A flammable map showing the flammable and non-flammable (ex. lakes) areas, stratas for harvesting, habitat map depending on land cover, disturbance map showing harvest and fire activities, a fire map showing the intensity of fires, an age map showing the age of the forests, maps of presence probability of two bird species (MOWA, RBNU) and a graph showing the Caribou population growth in the area. 

## Input data

The model is zipped with the needed data. It might also be fetched once the model starts running.
