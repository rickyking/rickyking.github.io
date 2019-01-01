---
title: "Shape File to Geojson in R"
date: 2015/06/12 09:11:44
tags: [data science, r]
categories: [R]
---

Currently, I am working on a project which requires visualize Polygons on the map. The problem that I have encountered is that in order to visualize the result in `leafletjs`, I need to convert the shape file to geojson format.

So the solution is to use [rgdal](http://cran.r-project.org/web/packages/rgdal/index.html) package to do the conversion.

The input is the shapefile, transformed to a `sp::SpatialPolygonsDataFrame`, and the output is a geojson file.

```r
# load library
library(rgdal)
library(sp)
library(maptools)
# read shapefile
P4S.latlon <- CRS("+proj=longlat +datum=WGS84")
shp <- readShapePoly("input/uga2/UGA_WGS_region", verbose=TRUE, proj4string=P4S.latlon)
# write geojson
geojson_file <- "path/to/file.json"
writeOGR(shp, geojson_file,layer = "geojson", driver = "GeoJSON")
```

The ultimate use case is to use shiny to render on live (but this is quite limited to the quantity of polygons), here is a function to do so:

```r
spToGeoJSON <- function(sp_obj){
  temp_file<-tempfile()
  writeOGR(sp_obj, temp_file,layer = "geojson", driver = "GeoJSON")
  geojs <- paste(readLines(temp_file), collapse=" ")
  file.remove(temp_file)
  return(geojs)
}
```

The function returns a string variable with `geojson` content.
