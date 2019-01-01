---
title: "Geocoding with R/Python"
date: 2015-05-28 10:15:40
tags: [data science, r, python, geo]
categories: [toolbox]
---

## What is geocoding

Geocoding and reverse geocoding are the process of turning the address or place name into geographic coordinates, vice versa.

More detailed definition: [wikipedia](http://en.wikipedia.org/wiki/Geocoding)

Simple illustration:

> Address --> Latitude,Longitude: Geocoding
> Latitude,Longitude --> Address: Reverse geocoding



## Implementation

The implementation is done by various providers, here is a [list](https://pypi.python.org/pypi/geocoder):

| Global            | Country           | Specialized     |
| :---------------- | :---------------- | :-------------- |
| [Google]          | [Yandex]          | [GeoOttawa]     |
| [Bing]            | [Geocoder.ca]     | [FreeGeoIP]     |
| [OpenStreetMap]   | [Baidu]           | [MaxMind]       |
| [HERE]            |                   | [What3Words]    |
| [TomTom]          |                   | [CanadaPost]    |
| [MapQuest]        |                   | [GeoNames]      |
| [OpenCage]        |                   |                 |
| [Yahoo]           |                   |                 |
| [ArcGIS]          |                   |                 |

So with these services, next step is to use script to access the service's API.

[Google]: https://developers.google.com/maps/documentation/geocoding/
[Bing]: http://msdn.microsoft.com/en-us/library/ff701714.aspx
[OpenStreetMap]: http://wiki.openstreetmap.org/wiki/Nominatim
[HERE]: https://developer.here.com/rest-apis/documentation/geocoder
[TomTom]: http://developer.tomtom.com/products/geocoding_api
[MapQuest]: http://www.mapquestapi.com/geocoding/
[OpenCage]: http://geocoder.opencagedata.com/api.html
[Yahoo]: https://developer.yahoo.com/boss/geo/
[ArcGIS]: https://developers.arcgis.com/rest/geocode/api-reference/geocoding-find.htm
[Yandex]: http://api.yandex.com/maps/doc/geocoder/desc/concepts/input_params.xml
[Geocoder.ca]: http://geocoder.ca/?api=1
[Baidu]: http://developer.baidu.com/map/index.php?title=webapi/guide/webservice-geocoding
[GeoOttawa]: http://maps.ottawa.ca/geoottawa/
[FreeGeoIP]: http://freegeoip.net/
[MaxMind]: https://www.maxmind.com/en/geolocation_landing
[What3Words]: http://developer.what3words.com/
[CanadaPost]: https://www.canadapost.ca/pca/
[GeoNames]: http://www.geonames.org/export/web-services.html

<!-- more -->

## Geocoding using R

Basically in R, two packages are implemented:

- [ggmap](http://cran.r-project.org/web/packages/ggmap/index.html)
- [geocodeHERE](https://github.com/corynissen/geocodeHERE)

### ggmap example

`ggmap` provides [Google Maps API](https://developers.google.com/maps/documentation/geocoding/):

```r
# invoke the geocode function
ggmap::geocode("New York")
# Information from URL : http://maps.googleapis.com/maps/api/geocode/json?address=New+York&sensor=false
#         lon      lat
# 1 -74.00594 40.71278
```

The output level can be chosen by invoking the parameter: `output = c("latlon", "latlona", "more", "all")`.

And the free tier query rate can be check via `geocodeQueryCheck(userType = "free")`.

*For the newly developed package `geocodeHERE`, please consult the github page.*

### Coding yourself in R

I have created a R function that uses [Bing] Geocoding service. The function import two packages: `httr` & `RJSONIO`. `httr` provides the `cURL` interface to do the `GET` action. `RJSONIO` is used to parse the returned json content. The function takes the `address` string and the authentication `key` as parameter input, then returns in data frame.

You can get your key from [here](https://msdn.microsoft.com/en-us/library/ff428642.aspx).

```r
geocoder <-
  function(addr, key = "Aq1OdgJVGKWyPuRcaIpgsG8pZcSzy0wLF31OVyY8sSq0sWA5npVn-MAAU9sVU97f") {
    require(httr)
    require(RJSONIO)
    reset_config()
    link <- "http://dev.virtualearth.net/"
    add_returned <- modify_url(link, path="REST/v1/Locations", query=list(location=addr,key=key))
    json <- try(fromJSON(content(add_returned, as ="text")))
    if (class(json) != "try-error") {
      if (json$statusCode == 200) {
        if (json$resourceSets[[1]]$estimatedTotal > 0) {
          coordinates <- data.frame(lat = json$resourceSets[[1]]$resources[[1]]$geocodePoints[[1]]$coordinates[1], lon = json$resourceSets[[1]]$resources[[1]]$geocodePoints[[1]]$coordinates[2])
          add_detailed <- json$resourceSets[[1]]$resources[[1]]$address
          add_detailed <- data.frame(t(json$resourceSets[[1]]$resources[[1]]$address))
          data <- cbind(addr, coordinates,add_detailed)
          rownames(data) <- NULL
        }
        else data <- data.frame(addr)
      }
      else {
        data <- data.frame(addr)
      }
    }
    else data <- data.frame(addr)
    rownames(data) <- NULL
    return(data)
  }
```

## Geocoding using python

In python, you have more choices other than create your own function to `GET` and parse the API.

Here are some wrapped packages:

- [geocoder](https://pypi.python.org/pypi/geocoder): wraps all services
- [geopy](https://pypi.python.org/pypi/geopy): wraps all services
- [pygeocoder](https://pypi.python.org/pypi/pygeocoder): only google
- [python-geocoder](https://pypi.python.org/pypi/python-geocoder/0.2): only google

The last two packages seem to be abandoned, at least not as powerful as first two packages.

`geocoder` is more simple and provides more useful returned values. The only problem is that `geocoder` seems to be python 2.x only.

`geopy` is python 2 & 3 ready and implements with object-oriented programming. And you can have more thorough controls.

Here is my sample code to read from `csv` file a column with header 'location' and return required value.

```python
import geocoder
import csv

keybing="Aq1OdgJVGKWyPuRcaIpgsG8pZcSzy0wLF31OVyY8sSq0sWA5npVn-MAAU9sVU97f"

rows = []
fieldnames = ['location', 'lat', 'lng', 'city', 'state', 'country']
with open('addr.csv') as f:
        reader = csv.DictReader(f, delimiter=';')
        for line in reader:
                g = geocoder.bing(line['location'], key=keybing)
                result = g.json
                result.update(line)
                rows.append(result)

with open("res.csv", "wb") as f:
        writer = csv.DictWriter(f, fieldnames=fieldnames, extrasaction='ignore')
        writer.writeheader()
        writer.writerows(rows)

```
