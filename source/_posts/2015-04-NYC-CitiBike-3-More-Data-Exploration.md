---
title: "NYC CitiBike [3] - More Data Exploration"
date: 2015-04-07 12:21:45
tags: [data science, open data, r, citibike]
categories: [R]
---

The previous two posts illustrate the extraction and exploration of trip data and weather data. In this section, I will illustrate importing the data of new membership subscription and the data of public holiday.

With the information of new membership subscription, I can recalculate the "active" user each day. This variable could well explain the number of trips, especially for customer user. And the public holiday could explain as well some strange behavior that are abnormal in the weekdays.

The libraries required are:


```r
library(plyr)  # require rbind.fill
library(stringr)
library(dplyr)
library(xts)
library(lubridate)
library(rvest)
library(ggplot2)
library(ggfortify)
```

<!-- more -->

## Getting new membership subscription

### Getting data
The membership data is aggregated at daily level which can be obtained via the [page](https://www.citibikenyc.com/system-data) - section "Citi Bike Daily Ridership and Membership Data". Click on the link of data wrapper and download the csv file. (No R script is created, hence all manual download)


### Data loading

Then we can read all the csv files and do the crunching:


```r
## GET csv file names
file2crunch <- dir("../input/clean", full.names = TRUE)
ind_crunch <- grep("data-", file2crunch)
file2crunch <- file2crunch[ind_crunch]
## READ data via ldply
dt_aggr <- ldply(file2crunch, function(x) {
  read.csv(x, stringsAsFactors=FALSE, check.names=FALSE)
})

# rename the columns
colnames(dt_aggr) <-
c(
"date"                   , "trips_per_day"                  ,
"cumulative_trip"        , "miles_per_day"                  ,
"cumulative_miles"       , "subscriber_total"               ,
"new_subscriber_per_day" , "new_customer_per_day_sum"       ,
"subscriber_total2"      , "new_customer_per_day_pass_1day" ,
"new_customer_per_day_pass_7days"
	)
```

There are two problems in the downloaded dataset:

- the data of Oct - Dec 2013 only has the sum of 1 day pass and 7 days pass, no seperated volume.
- the data of Oct - Dec 2014 has some formating problem, which requires manual correction.

Since we only use the data of 2014, we ignore the first problem.

### Data preprocessing

Some preprocessing is required.


```r
## merge columns
dt_aggr$subscriber_total[is.na(dt_aggr$subscriber_total)] <- dt_aggr$subscriber_total2[is.na(dt_aggr$subscriber_total)]
## calculate sum of pass subscription
ind_na <- is.na(dt_aggr$new_customer_per_day_sum)
dt_aggr$new_customer_per_day_pass_7days[dt_aggr$new_customer_per_day_pass_7days=="undefined"] <- "0"
dt_aggr$new_customer_per_day_pass_7days <- as.numeric(dt_aggr$new_customer_per_day_pass_7days)

dt_aggr$new_customer_per_day_sum[ind_na] <- dt_aggr$new_customer_per_day_pass_1day[ind_na] + dt_aggr$new_customer_per_day_pass_7days[ind_na]

## Formatting problem
dt_aggr$cumulative_miles <- gsub(",","",dt_aggr$cumulative_miles)
dt_aggr$cumulative_miles <- gsub("\t","",dt_aggr$cumulative_miles)
dt_aggr$cumulative_miles <- as.numeric(dt_aggr$cumulative_miles)
## Parse and reorder by date
dt_aggr$date_parsed <- mdy(dt_aggr$date)
ordered <- order(dt_aggr$date_parsed)
dt_aggr <- dt_aggr[ordered,]
```

Here is some descriptive plot:


```r
qplot(dt_aggr$date_parsed, dt_aggr$subscriber_total, geom="line", main="Cumulative Subscriber")
```

![](unnamed-chunk-4-1.png)

```r
qplot(dt_aggr$date_parsed,dt_aggr$new_subscriber_per_day, geom="line", main="New annual membership")
```

![](unnamed-chunk-4-2.png)

```r
qplot(dt_aggr$date_parsed, dt_aggr$new_customer_per_day_sum, geom="line", main="New 24H/7D pass")
```

![](unnamed-chunk-4-3.png)

```r
qplot(dt_aggr$date_parsed, dt_aggr$new_customer_per_day_pass_1day, geom="line", main="New 24H pass")
```

![](unnamed-chunk-4-4.png)

```r
qplot(dt_aggr$date_parsed, dt_aggr$new_customer_per_day_pass_7days, geom="line", main="New 7D pass")
```

![](unnamed-chunk-4-5.png)

### Data transforming

First we transform the data to calculate the active membership/pass.

Active annual subscriber:


```r
dt_aggr$subscriber_exit <- lag(dt_aggr$new_subscriber_per_day, 365)[1:nrow(dt_aggr)]
dt_aggr$subscriber_exit[is.na(dt_aggr$subscriber_exit)] <- 0
dt_aggr$subscriber_active <- cumsum(dt_aggr$new_subscriber_per_day) - cumsum(dt_aggr$subscriber_exit)
# plot(dt_aggr$date_parsed, dt_aggr$subscriber_active, type="l", main="Active annual subscriber")
dt_xts <- xts(dt_aggr$subscriber_active, order.by=dt_aggr$date_parsed)
autoplot(dt_xts, main="Active annual subscriber", facet=FALSE)
```

![](unnamed-chunk-5-1.png)

Active customer only calculated for 2014:


```r
dt_aggr$customer_pass_1day_exit <- lag(dt_aggr$new_customer_per_day_pass_1day, 1)
dt_aggr$customer_pass_7days_exit <- lag(dt_aggr$new_customer_per_day_pass_7days, 7)
dt_aggr$customer_pass_sum_exit <- dt_aggr$customer_pass_1day_exit + dt_aggr$customer_pass_7days_exit

ind_year <- year(dt_aggr$date_parsed)==2014
ind <- is.na(dt_aggr$customer_pass_sum_exit) & year(dt_aggr$date_parsed)==2014

dt_aggr$customer_pass_sum_exit[ind] <- 0
dt_aggr$new_customer_per_day_pass_1day[is.na(dt_aggr$new_customer_per_day_pass_1day)] <- 0
dt_aggr$new_customer_per_day_pass_7days[is.na(dt_aggr$new_customer_per_day_pass_7days)] <- 0
dt_aggr$customer_pass_active <- 0

dt_aggr$customer_pass_active[ind_year] <- cumsum(dt_aggr$new_customer_per_day_pass_1day[ind_year]) +
cumsum(dt_aggr$new_customer_per_day_pass_7days[ind_year]) - cumsum(dt_aggr$customer_pass_sum_exit[ind_year])
#PLOT
dt_xts <- xts(cbind("overall"=dt_aggr$customer_pass_active, "24H"=dt_aggr$new_customer_per_day_pass_1day,
  "7days"=dt_aggr$new_customer_per_day_pass_7days), order.by=dt_aggr$date_parsed)
autoplot(dt_xts, facets = FALSE)
```

![](unnamed-chunk-6-1.png)

Prepare the data to save:


```r
dt_aggr <- tbl_df(dt_aggr) %>%
  mutate(year=year(date_parsed), month=month(date_parsed), mday=mday(date_parsed), wday=wday(date_parsed)) %>%
	filter(year==2014)
```

## Getting public holiday data

The public holiday data is getting through this [site](http://www.officeholidays.com/countries/usa/regional.php?list_year=2014&list_region=New%20York). We will stick to R to do some web scrapping. The package required is `rvest`.


```r
## Base information
baseURL <- "http://www.officeholidays.com/countries/usa/regional.php"
year <- 2014
region <- "New York"
## Get page html
res <- html(baseURL, query=list(list_year=2014, list_region=region))
## Get table, 1st one
dt_holiday <- html_table(res, fill=TRUE)[[1]]
## manually remove none public holiday
dt_holiday <- dt_holiday[-c(3,4,6,8,12,15,17),]
## prepare for date transforming
dt_holiday$Date <- gsub("January", "01", dt_holiday$Date)
dt_holiday$Date <- gsub("February", "02", dt_holiday$Date)
dt_holiday$Date <- gsub("March", "03", dt_holiday$Date)
dt_holiday$Date <- gsub("April", "04", dt_holiday$Date)
dt_holiday$Date <- gsub("May", "05", dt_holiday$Date)
dt_holiday$Date <- gsub("June", "06", dt_holiday$Date)
dt_holiday$Date <- gsub("July", "07", dt_holiday$Date)
dt_holiday$Date <- gsub("Auguste", "08", dt_holiday$Date)
dt_holiday$Date <- gsub("September", "09", dt_holiday$Date)
dt_holiday$Date <- gsub("October", "10", dt_holiday$Date)
dt_holiday$Date <- gsub("November", "11", dt_holiday$Date)
dt_holiday$Date <- gsub("December", "12", dt_holiday$Date)
## Parse date
dt_holiday$date_parsed <- lubridate::ymd(paste0("2014 ", dt_holiday$Date))
## Create cbind ready dataframe
dt_holiday_vec <- data.frame(date=seq(ymd("2014-01-01"), ymd("2014-12-31"), by="day"))
dt_holiday_vec$holiday <- FALSE
dt_holiday_vec$holiday[dt_holiday_vec$date %in% dt_holiday$date_parsed] <- TRUE
## the data frame is ready to save
```
