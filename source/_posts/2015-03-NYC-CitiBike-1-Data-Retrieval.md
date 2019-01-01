---
title: "NYC CitiBike [1] - Data Retrieval"
date: 2015-03-23 15:16:01
tags: [data science, open data, r, citibike]
categories: [R]
---

New York city's Citi Bike provides an [open data](https://www.citibikenyc.com/system-data). All files are zipped and can be found over the site.

The objectif is to use the dataset to do explore some interesting insight of the NYC citibike users' usage patern.

The current documentation is to make the data retrieval reproducible.

The pipeline of data retrieval is:

1. Scrapper the page which lists all the zip files
2. Get the url of all the zip files
3. Download the zip files and unzip
4. Construct a data frame
5. Due to large volume of data, the data is stored in SQLite and only subset a trunk

The package required:


```r
library(plyr)
library(weatherData)
library(dplyr)
library(stringr)
library(lubridate)
```

<!-- more -->

## Systeme Data extract - transform - load

### Data extracting

The data is downloaded via the site of [citibikenyc.com](http://www.citibikenyc.com/system-data). We have the detailed information of the trips from july 2013 to december 2014. The data includes:

- Trip Duration (seconds)
- Start Time and Date
- Stop Time and Date
- Start Station Name
- End Station Name
- Station ID
- Station Lat/Long
- Bike ID
- User Type (Customer = 24-hour pass or 7-day pass user; Subscriber = Annual Member)
- Gender (Zero=unknown; 1=male; 2=female)
- Year of Birth


```r
# install.packages("rvest")
library(rvest)

# httr::set_config(httr::config(httr::use_proxy("defrceprx01.ey.net", 8080)))
page <- html("https://www.citibikenyc.com/system-data")

URL_list <- page %>% html_nodes("#system-data a") %>% html_attr("href")

ind_zip <- grep("zip", URL_list)

download_url <- str_trim(URL_list[ind_zip])

l_ply(download_url, function(x) {
    if (!file.exists(paste0(getwd(),"/input/raw/", basename(x)))) {
        download.file(x, destfile=paste0(getwd(),"/input/raw/", basename(x)), method="curl")
    }
})

file2unzip <- dir("input/raw", full.names=TRUE)
lapply(file2unzip, unzip, exdir="input/clean")
```

### Data transforming

A script will try to retrieve the data from the site and unzip the zipped csv file. Once the files are downloaded and unzipped, the csv files will be read by `ldply` and create a aggregated data frame.

Due to the size of the data, a SQLite database is created via script R by using package `dplyr` (this operation could be done by command line tool of SQLite).

Some variables are also created in order to extract maximum information:

- convert start/end datetime into POXIct and extract information of "year", "month", "month day", "week day", "year day", "hour", "minute", "second", and "isoweek"
- calculate the age of the rider
- calculate the `direct` distance of each ride


```r
##----------------------------------------------------
#
#           function
#
##----------------------------------------------------
isoyearweek <- function (x)
{
  xday <- ISOdate(year(x), month(x), day(x), tz = tz(x))
  dn <- 1 + (wday(x) + 5)%%7
  nth <- xday + ddays(4 - dn)
  jan1 <- ISOdate(year(nth), 1, 1, tz = tz(x))
  weeknum <- 1 + (nth - jan1)%/%ddays(7)
  isoyear <- year(nth)
  paste0(isoyear, sprintf("%02d", weeknum))
}

##----------------------------------------------------
#
#           find all unzip files
#
##----------------------------------------------------
file2read <- dir("input/clean", full.names=TRUE)
file2read

# name2match <- regexpr("[[:digit:]]+",file2read)
# regmatches(file2read, name2match)

##----------------------------------------------------
#
#           read and construct data frame
#
##----------------------------------------------------

dt_bike <- ldply(file2read, function(x) {
    dt <- read.csv(x, encoding="UTF-8", stringsAsFactors=FALSE)
    cat("this is file: ", x, "\n")
    if (substr(dt$starttime[1], 1, 4) %in% seq(2013, 2016, 1)) {
        dt$starttime_formated <- as.POSIXct(dt$starttime, tz="UTC", format="%Y-%m-%d %H:%M:%S")
        dt$stoptime_formated <- as.POSIXct(dt$stoptime, tz="UTC", format="%Y-%m-%d %H:%M:%S")
    } else {
        dt$starttime_formated <- as.POSIXct(dt$starttime, tz="UTC", format="%m/%d/%Y %H:%M:%S")
        dt$stoptime_formated <- as.POSIXct(dt$stoptime, tz="UTC", format="%m/%d/%Y %H:%M:%S")
    }
    dt$year <- year(dt$starttime_formated)
        cat("Check: ", dt$year[1], "\n")
    dt$month <- month(dt$starttime_formated)
        cat("Check: ", dt$month[1], "\n")
    dt$isoyearweek <- isoyearweek(dt$starttime_formated)
    dt$mday <- mday(dt$starttime_formated)
    dt$wday <- wday(dt$starttime_formated)
    dt$yday <- yday(dt$starttime_formated)
        cat("Check: ", dt$day[1], "\n")
    dt$hour <- hour(dt$starttime_formated)
        cat("Check: ", dt$hour[1], "\n")
    dt$minute <- minute(dt$starttime_formated)
        cat("Check: ", dt$minute[1], "\n")
    dt$second <- second(dt$starttime_formated)
        cat("Check: ", dt$second[1], "\n")
    dt$birth.year <- as.numeric(dt$birth.year)
        cat("Check: ", dt$birth.year[1], "\n")
    return(dt)
}, .progress="tk")

##----------------------------------------------------
#
#           new variable
#
##----------------------------------------------------
dt_bike$age <- dt_bike$year - dt_bike$birth.year

distCalc <- function(p1, p2, r = 6378137){
    # browser()
    toRad <- pi/180
    p1 <- as.matrix(p1) * toRad
    p2 <- as.matrix(p2) * toRad

    dLat <- p2[, 2] - p1[, 2]
    dLon <- p2[, 1] - p1[, 1]
    a <- sin(dLat/2) * sin(dLat/2) + cos(p1[, 2]) * cos(p2[, 2]) *
        sin(dLon/2) * sin(dLon/2)
    dist <- 2 * atan2(sqrt(a), sqrt(1 - a)) * r
    return(dist)
}
dt_bike$tripdistance <- distCalc(cbind(dt_bike$start.station.longitude, dt_bike$start.station.latitude), cbind(dt_bike$end.station.longitude, dt_bike$end.station.latitude))
dt_bike[1,]

##----------------------------------------------------
#
#           save
#
##----------------------------------------------------
if (!(file.exists("input/rds"))) dir.create("input/rds")
saveRDS(dt_bike, file="input/rds/dt_bike.rds")
```

### Data loading & saving

Once data is clean, we will the following code to construct a SQLite database in order to store the data and can be easily subset via `dplyr`.


```r
##----------------------------------------------------
#
#           save df to sqlite
#
##----------------------------------------------------
db_bike <- src_sqlite("input/rds/dt_bike_clean.sqlite3", create = T)
db_bike

bike_sqlite <- copy_to(db_bike, dt_bike, temporary = FALSE,
    indexes=list(c("year", "month", "mday","wday", "hour", "minute"), "start.station.id", "end.station.id", "bikeid"))
```

## Getting weather data

In order to make the data more explorable, we need to get more information of surrounding environment. Here we can find is weather.

By using the pacakge `weatherData`, the whole year weather can be easily obtained.

Here is the code to construct a data frame with weather information of new york city:


```r
# get new york city weather data for a year: KNYC (central park)
required_day <- seq(as.Date("2013-01-01"), as.Date("2014-12-31"), by ="day")
required_day <- as.character(required_day)
dt_knyc_2013_2014 <- ldply(required_day, function(x) {
  getDetailedWeather("KNYC", x, opt_all_columns=TRUE)
}, .progress="text")

str(dt_knyc_2013_2014)
tail(dt_knyc_2013_2014)

saveRDS(dt_knyc_2013_2014, file="input/rds/dt_knyc_2013_2014.rds")
```
