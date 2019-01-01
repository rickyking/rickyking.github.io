---
title: "NYC CitiBike [5] - Modeling"
date: 2015-05-06 13:20:24
tags: [data science, open data, r, citibike]
categories: [R]
---

After considering the modeling framework, the next step is to realize the analysis.

As suggested in the previous schemas, in order to create the predictive model to predict daily subscriber/pass user, number of trips, we are going to combine several source of data into one `dataframe`. Then running simple `randomForest` algorithm and `GAM` via `caret` package (machine learning for `R`).

Here is the required library:

```r
library(plyr)  # require rbind.fill
library(stringr)
library(dplyr)
library(dygraphs)
library(xts)
library(mgcv)
library(lubridate)
library(caret)
library(ggfortify)
```

<!-- more -->

## New Subscriber / pass user

### Combining data

The base dataset comes from [CitiBike System Data](https://www.citibikenyc.com/system-data), and for this part, I am using the "Citi Bike Daily Ridership and Membership Data". The data is aggregated in daily level which should be sufficient. The data retrieving and loading script could be found [here](http://jinyi.me/2015/04/NYC-CitiBike-3-More-Data-Exploration.htm#Getting_data).

Let us begin the data wrangling:

```r
# read the aggregated dataset
dt_aggr <- readRDS(file="../input/rds/dt_aggr.rds")
# add season variable
dt_aggr$season <- NULL
dt_aggr$season[dt_aggr$month %in% c(9,10,11)] <- "fall"
dt_aggr$season[dt_aggr$month %in% c(12,1,2)] <- "winter"
dt_aggr$season[dt_aggr$month %in% c(3,4,5)] <- "spring"
dt_aggr$season[dt_aggr$month %in% c(6,7,8)] <- "summer"
# load weather data
dt_weather_aggr <- readRDS("../input/rds/dt_knyc_2014_aggr.rds")
dt_weather_aggr$key <- as.character(floor_date(dt_weather_aggr$Date, unit="hour"))
dt_weather_aggr <- dt_weather_aggr[(!duplicated(dt_weather_aggr$key)),]
dt_weather_aggr$Precipitationmm <- as.numeric(dt_weather_aggr$Precipitationmm)
dt_weather_aggr$Precipitationmm[is.na(dt_weather_aggr$Precipitationmm)] <- 0
# join aggregated data and weather
dt_join <- bind_cols(dt_aggr, dt_weather_aggr[,c("Mean_TemperatureC", "CloudCover", "Min_TemperatureC", "Max_TemperatureC", "Precipitationmm")])
# GET public holiday
dt_holiday <- readRDS("../input/rds/dt_holiday_2014.rds")
# join them all
dt_join <- bind_cols(dt_join, dt_holiday["holiday"])

# data preparation for modeling
dt_join$season <- factor(dt_join$season)
dt_join$season_num <- as.numeric(dt_join$season)
dt_join$CloudCover[is.na(dt_join$CloudCover)] <- 0
dt_join <- tbl_df(dt_join) %>% dplyr::select(- subscriber_total2)
## complete cases
dt_complete <- dt_join[complete.cases(dt_join),]
dt_complete$season <- as.factor(dt_complete$season)
dt_complete$month <- as.factor(dt_complete$month)
dt_complete$wday <- as.factor(dt_complete$wday)
dt_complete$mday <- as.factor(dt_complete$mday)
dt_complete$holiday <- as.factor(dt_complete$holiday)
```

### Correlation exploration

Next let's visualize the correlation between each features:

```r
featurePlot(dt_join[,c("month", "wday", "mday", "Mean_TemperatureC","holiday" ,"season_num", "CloudCover","Max_TemperatureC", "Min_TemperatureC", "Precipitationmm")],  dt_join$new_subscriber_per_day, main="New Subscriber")
```

{% asset_img unnamed-chunk-3-1.png %}

```r
featurePlot(dt_join[,c("month", "wday", "mday", "Mean_TemperatureC","holiday" ,"season_num", "CloudCover","Max_TemperatureC", "Min_TemperatureC", "Precipitationmm")],  dt_join$new_customer_per_day_pass_1day, main="New 24-hour pass")
```

{% asset_img unnamed-chunk-3-2.png %}

```r
featurePlot(dt_join[,c("month", "wday", "mday", "Mean_TemperatureC","holiday" ,"season_num", "CloudCover","Max_TemperatureC", "Min_TemperatureC", "Precipitationmm")],  dt_join$new_customer_per_day_pass_7days, main="New 7-day pass")
```

{% asset_img unnamed-chunk-3-3.png %}

From the plots, we can see some clear correlation between the predictor and outcome variable. As observed in the plot, the following variables will be in the model: month, season, day of the week, Cloud Cover, max temperature,  average temperature, holiday, precipitation. As opposed, the day of the month and min temperature are dropped.

### Modeling

Now we are going to model the outcome variable with the selected predictor.

Some tips for modeling with `caret` package:

1. With the model object, we can use `caret::varImp()` function to print out the list of predictors' importance.
2. Use `caret::trainControl()`, we can fine tuning the training part. For example, changing the methode for paramter searching, creating the prediction bounds to avoid unexpected outcome, etc...

First GAM model for Subscriber:


```r
fitControl <- trainControl(method = "timeslice",
    initialWindow=90,
    horizon =30,
    fixedWindow =FALSE,
    predictionBounds= c(min(dt_complete$new_subscriber_per_day), NA))


dt_complete$season <- as.factor(dt_complete$season)
gamfit <- train(new_subscriber_per_day ~ month+season+(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+holiday+Precipitationmm,
                 data = dt_complete,
                 method = "gam",
                 trControl = fitControl,
                 verbose = TRUE
                 )
gamfit
```

```
## Generalized Additive Model using Splines
##
## 365 samples
##  28 predictors
##
## No pre-processing
## Resampling: Rolling Forecasting Origin Resampling (30 held-out with no fixed window)
##
## Summary of sample sizes: 90, 91, 92, 93, 94, 95, ...
##
## Resampling results across tuning parameters:
##
##   select  RMSE      Rsquared   RMSE SD   Rsquared SD
##   FALSE   34.30883  0.3690622  11.58026  0.1758098
##    TRUE   34.59588  0.3622272  11.69093  0.1735027
##
## Tuning parameter 'method' was held constant at a value of GCV.Cp
## RMSE was used to select the optimal model using  the smallest value.
## The final values used for the model were select = FALSE and method
##  = GCV.Cp.
```

```r
summary(gamfit$final)
```

```
##
## Family: gaussian
## Link function: identity
##
## Formula:
## .outcome ~ month2 + month3 + month4 + month5 + month6 + month7 +
##     month8 + month9 + month10 + month11 + month12 + seasonspring +
##     seasonsummer + seasonwinter + wday2 + wday3 + wday4 + wday5 +
##     wday6 + wday7 + CloudCover + s(Mean_TemperatureC) + s(Max_TemperatureC) +
##     s(Precipitationmm)
##
## Parametric coefficients:
##              Estimate Std. Error t value Pr(>|t|)
## (Intercept)   63.9904     3.3012  19.384  < 2e-16 ***
## month2        -7.5645     6.4945  -1.165  0.24494
## month3        -5.6178     4.8733  -1.153  0.24982
## month4        35.2866     3.9034   9.040  < 2e-16 ***
## month5        26.4922     4.7600   5.566 5.34e-08 ***
## month6        32.6786     3.6943   8.846  < 2e-16 ***
## month7         6.0140     3.9588   1.519  0.12966
## month8        -8.7629     3.7345  -2.347  0.01953 *
## month9        12.7409     5.2262   2.438  0.01529 *
## month10       -8.3579     4.3893  -1.904  0.05774 .
## month11      -31.8047     4.7971  -6.630 1.34e-10 ***
## month12      -33.9156     6.9852  -4.855 1.84e-06 ***
## seasonspring  56.1609     2.2979  24.441  < 2e-16 ***
## seasonsummer  29.9297     4.1357   7.237 3.13e-12 ***
## seasonwinter   5.3214     6.2305   0.854  0.39366
## wday2         23.6845     4.7229   5.015 8.61e-07 ***
## wday3         14.1847     4.7850   2.964  0.00325 **
## wday4          9.6321     4.7380   2.033  0.04284 *
## wday5          2.5874     4.6907   0.552  0.58160
## wday6         -6.3784     4.6778  -1.364  0.17362
## wday7        -15.8564     4.6764  -3.391  0.00078 ***
## CloudCover    -1.8470     0.5734  -3.221  0.00140 **
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Approximate significance of smooth terms:
##                        edf Ref.df      F  p-value
## s(Mean_TemperatureC) 3.330  4.278  1.678 0.149354
## s(Max_TemperatureC)  5.241  6.346  2.026 0.058064 .
## s(Precipitationmm)   1.000  1.000 14.235 0.000189 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Rank: 46/49
## R-sq.(adj) =  0.856   Deviance explained = 86.7%
## GCV =    596  Scale est. = 549.34    n = 365
```

```r
pred <- predict(gamfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete["new_subscriber_per_day"], pred), order.by = dt_complete$date_parsed)
autoplot(dt_xts, facet=FALSE, main="[GAM] Real vs. Prediction: New Subscriber")
```

{% asset_img unnamed-chunk-4-1.png %}

```r
# varImp(gamfit$finalModel)
# save mod in rds
# saveRDS(gamfit, "input/mod/MOD_gam_subs_new_user.rds")
```

Next `randomForest` model for subscriber:


```r
fitControl <- trainControl(method = "oob",
    predictionBounds= c(min(dt_complete$new_subscriber_per_day), NA))
dt_complete$season <- as.factor(dt_complete$season)
rdForestfit <- train(new_subscriber_per_day ~ month+season*(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+Precipitationmm+holiday,
                 data = dt_complete,
                 method = "rf",
                 trControl = fitControl,
                 verbose = TRUE,
                 predictionBounds=c(TRUE, FALSE),
                 importance = TRUE)
```

```
## Loading required package: randomForest
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
##
## Attaching package: 'randomForest'
##
## The following object is masked from 'package:dplyr':
##
##     combine
```

```r
rdForestfit
```

```
## Random Forest
##
## 365 samples
##  28 predictors
##
## No pre-processing
## Resampling results across tuning parameters:
##
##   mtry  RMSE      Rsquared
##    2    32.28844  0.7262173
##   22    24.29799  0.8449569
##   43    24.87653  0.8374858
##
## RMSE was used to select the optimal model using  the smallest value.
## The final value used for the model was mtry = 22.
```

```r
rdForestfit$finalModel
```

```
##
## Call:
##  randomForest(x = x, y = y, mtry = param$mtry, importance = TRUE,      verbose = TRUE, predictionBounds = ..2)
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 22
##
##           Mean of squared residuals: 582.0263
##                     % Var explained: 84.72
```

```r
pred <- predict(rdForestfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$new_subscriber_per_day, pred), order.by = dt_complete$date_parsed)
autoplot(dt_xts, facet=FALSE, main="[Random Forest] Real vs. Prediction: New Subscriber")
```

{% asset_img unnamed-chunk-5-1.png %}

```r
# varImp(rdForestfit$finalModel)
# save mod in rds
# saveRDS(rdForestfit, "input/mod/MOD_rf_subs_new_user.rds")
```

The following codes will model the new 24h pass and new 7-day pass per day. Same as those for subscriber.


```r
# Pass 24H -----------------------------
## GAM
fitControl <- trainControl(method = "timeslice",
    initialWindow=90,
    horizon =30,
    fixedWindow =FALSE,
    predictionBounds= c(min(dt_complete$new_customer_per_day_pass_1day), NA))
dt_complete$season <- as.factor(dt_complete$season)
gamfit <- train(new_customer_per_day_pass_1day ~ month+season+(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+Precipitationmm+holiday,
                 data = dt_complete,
                 method = "gam",
                 trControl = fitControl,
                 verbose = TRUE
                 )
gamfit
```

```
## Generalized Additive Model using Splines
##
## 365 samples
##  28 predictors
##
## No pre-processing
## Resampling: Rolling Forecasting Origin Resampling (30 held-out with no fixed window)
##
## Summary of sample sizes: 90, 91, 92, 93, 94, 95, ...
##
## Resampling results across tuning parameters:
##
##   select  RMSE      Rsquared   RMSE SD   Rsquared SD
##   FALSE   530.2557  0.6120520  209.4885  0.1804140
##    TRUE   516.0784  0.6265652  192.9839  0.1607643
##
## Tuning parameter 'method' was held constant at a value of GCV.Cp
## RMSE was used to select the optimal model using  the smallest value.
## The final values used for the model were select = TRUE and method = GCV.Cp.
```

```r
summary(gamfit$final)
```

```
##
## Family: gaussian
## Link function: identity
##
## Formula:
## .outcome ~ month2 + month3 + month4 + month5 + month6 + month7 +
##     month8 + month9 + month10 + month11 + month12 + seasonspring +
##     seasonsummer + seasonwinter + wday2 + wday3 + wday4 + wday5 +
##     wday6 + wday7 + CloudCover + s(Mean_TemperatureC) + s(Max_TemperatureC) +
##     s(Precipitationmm)
##
## Parametric coefficients:
##              Estimate Std. Error t value Pr(>|t|)
## (Intercept)  1239.491     53.185  23.305  < 2e-16 ***
## month2         35.747    103.788   0.344 0.730743
## month3       -133.287     78.061  -1.707 0.088651 .
## month4        122.871     63.093   1.947 0.052309 .
## month5        273.238     77.168   3.541 0.000455 ***
## month6        120.083     59.635   2.014 0.044839 *
## month7         86.840     63.436   1.369 0.171929
## month8        165.805     60.346   2.748 0.006326 **
## month9        296.307     84.555   3.504 0.000519 ***
## month10       267.440     70.481   3.794 0.000175 ***
## month11        60.887     77.383   0.787 0.431938
## month12       171.416    111.307   1.540 0.124488
## seasonspring  262.822     36.423   7.216 3.55e-12 ***
## seasonsummer  372.729     66.228   5.628 3.83e-08 ***
## seasonwinter  -20.694    100.079  -0.207 0.836312
## wday2        -786.055     75.650 -10.391  < 2e-16 ***
## wday3        -908.215     76.375 -11.891  < 2e-16 ***
## wday4        -840.871     76.131 -11.045  < 2e-16 ***
## wday5        -803.447     75.687 -10.615  < 2e-16 ***
## wday6        -656.795     75.304  -8.722  < 2e-16 ***
## wday7         113.261     75.137   1.507 0.132641
## CloudCover    -47.156      9.118  -5.172 3.98e-07 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Approximate significance of smooth terms:
##                        edf Ref.df     F p-value
## s(Mean_TemperatureC) 4.417      9 0.940  0.0120 *
## s(Max_TemperatureC)  2.213      9 0.865  0.0020 **
## s(Precipitationmm)   1.454      9 0.516  0.0329 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Rank: 46/49
## R-sq.(adj) =  0.778   Deviance explained = 79.4%
## GCV = 1.5456e+05  Scale est. = 1.4309e+05  n = 365
```

```r
pred <- predict(gamfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$new_customer_per_day_pass_1day, pred), order.by = dt_complete$date_parsed)
autoplot(dt_xts, facet=FALSE, main="[GAM] Real vs. Prediction: New 24h pass")
```

{% asset_img unnamed-chunk-6-1.png %}

```r
# varImp(gamfit$finalModel)
# save mod in rds
# saveRDS(gamfit, "input/mod/MOD_gam_pass24h_new_user.rds")

## randomForest
fitControl <- trainControl(method = "oob",
    predictionBounds= c(min(dt_complete$new_customer_per_day_pass_1day), NA))
dt_complete$season <- as.factor(dt_complete$season)
rdForestfit <- train(new_customer_per_day_pass_1day ~ month+season*(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+Precipitationmm+holiday,
                 data = dt_complete,
                 method = "rf",
                 trControl = fitControl,
                 verbose = TRUE,
                 predictionBounds=c(TRUE, FALSE),
                 importance = TRUE)
rdForestfit
```

```
## Random Forest
##
## 365 samples
##  28 predictors
##
## No pre-processing
## Resampling results across tuning parameters:
##
##   mtry  RMSE      Rsquared
##    2    480.3382  0.6415306
##   22    330.7953  0.8299892
##   43    321.2195  0.8396897
##
## RMSE was used to select the optimal model using  the smallest value.
## The final value used for the model was mtry = 43.
```

```r
rdForestfit$finalModel
```

```
##
## Call:
##  randomForest(x = x, y = y, mtry = param$mtry, importance = TRUE,      verbose = TRUE, predictionBounds = ..2)
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 43
##
##           Mean of squared residuals: 97089.65
##                     % Var explained: 84.92
```

```r
pred <- predict(rdForestfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$new_customer_per_day_pass_1day, pred), order.by = dt_complete$date_parsed)
autoplot(dt_xts, facet=FALSE, main="[Random Forest] Real vs. Prediction: New 24h pass")
```

{% asset_img unnamed-chunk-6-2.png %}

```r
# varImp(rdForestfit$finalModel)
# save mod in rds
# saveRDS(rdForestfit, "input/mod/MOD_rf_pass24h_new_user.rds")

# Pass 7-day -----------------------------
## GAM
fitControl <- trainControl(method = "timeslice",
    initialWindow=90,
    horizon =30,
    fixedWindow =FALSE,
    predictionBounds= c(min(dt_complete$new_customer_per_day_pass_7days), NA))
dt_complete$season <- as.factor(dt_complete$season)
gamfit <- train(new_customer_per_day_pass_7days ~ month+season+(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+Precipitationmm+holiday,
                 data = dt_complete,
                 method = "gam",
                 trControl = fitControl,
                 verbose = TRUE
                 )
gamfit
```

```
## Generalized Additive Model using Splines
##
## 365 samples
##  28 predictors
##
## No pre-processing
## Resampling: Rolling Forecasting Origin Resampling (30 held-out with no fixed window)
##
## Summary of sample sizes: 90, 91, 92, 93, 94, 95, ...
##
## Resampling results across tuning parameters:
##
##   select  RMSE      Rsquared   RMSE SD   Rsquared SD
##   FALSE   25.78840  0.4298846  11.27941  0.1998058
##    TRUE   25.50191  0.4455575  10.81393  0.2038425
##
## Tuning parameter 'method' was held constant at a value of GCV.Cp
## RMSE was used to select the optimal model using  the smallest value.
## The final values used for the model were select = TRUE and method = GCV.Cp.
```

```r
summary(gamfit$final)
```

```
##
## Family: gaussian
## Link function: identity
##
## Formula:
## .outcome ~ month2 + month3 + month4 + month5 + month6 + month7 +
##     month8 + month9 + month10 + month11 + month12 + seasonspring +
##     seasonsummer + seasonwinter + wday2 + wday3 + wday4 + wday5 +
##     wday6 + wday7 + CloudCover + s(Mean_TemperatureC) + s(Max_TemperatureC) +
##     s(Precipitationmm)
##
## Parametric coefficients:
##              Estimate Std. Error t value Pr(>|t|)
## (Intercept)   62.2822     2.2304  27.925  < 2e-16 ***
## month2        -1.6901     4.1556  -0.407  0.68449
## month3       -15.5660     3.1597  -4.926 1.32e-06 ***
## month4         0.7796     2.5584   0.305  0.76077
## month5        26.4211     3.0990   8.526 5.34e-16 ***
## month6        13.5691     2.4059   5.640 3.63e-08 ***
## month7         4.9838     2.5468   1.957  0.05120 .
## month8         6.5867     2.4337   2.706  0.00715 **
## month9        24.0003     3.4025   7.054 1.01e-11 ***
## month10       18.4406     2.8668   6.433 4.35e-10 ***
## month11       -7.3938     3.1269  -2.365  0.01862 *
## month12        2.4032     4.5110   0.533  0.59456
## seasonspring  11.6347     1.4798   7.862 5.25e-14 ***
## seasonsummer  25.1395     2.6698   9.416  < 2e-16 ***
## seasonwinter  -9.5391     3.9943  -2.388  0.01749 *
## wday2          5.0210     3.0647   1.638  0.10229
## wday3         -2.9347     3.1096  -0.944  0.34599
## wday4         -1.3634     3.0808  -0.443  0.65839
## wday5         -5.8651     3.0569  -1.919  0.05588 .
## wday6         -3.0111     3.0352  -0.992  0.32188
## wday7          1.4640     3.0419   0.481  0.63064
## CloudCover    -2.5178     0.4106  -6.132 2.45e-09 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Approximate significance of smooth terms:
##                        edf Ref.df     F  p-value
## s(Mean_TemperatureC) 2.963      8 0.951     0.01 *
## s(Max_TemperatureC)  4.597      8 3.020 2.39e-07 ***
## s(Precipitationmm)   4.470      9 5.639 6.62e-11 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Rank: 46/49
## R-sq.(adj) =  0.905   Deviance explained = 91.2%
## GCV = 252.99  Scale est. = 231.48    n = 365
```

```r
pred <- predict(gamfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$new_customer_per_day_pass_7days, pred), order.by = dt_complete$date_parsed)
autoplot(dt_xts, facet=FALSE, main="[GAM] Real vs. Prediction: New 7-day pass")
```

{% asset_img unnamed-chunk-6-3.png %}

```r
# varImp(gamfit$finalModel, scale=FALSE)
# save mod in rds
# saveRDS(gamfit, "input/mod/MOD_gam_pass7d_new_user.rds")

## randomForest
fitControl <- trainControl(method = "oob",
    predictionBounds= c(min(dt_complete$new_customer_per_day_pass_7days), NA))
rdForestfit <- train(new_customer_per_day_pass_7days ~ month+season*(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+Precipitationmm+holiday,
                 data = dt_complete,
                 method = "rf",
                 trControl = fitControl,
                 verbose = TRUE,
                 predictionBounds=c(TRUE, FALSE),
                 importance = TRUE)
rdForestfit
```

```
## Random Forest
##
## 365 samples
##  28 predictors
##
## No pre-processing
## Resampling results across tuning parameters:
##
##   mtry  RMSE      Rsquared
##    2    21.38673  0.8108644
##   22    14.69838  0.9106646
##   43    14.86667  0.9086072
##
## RMSE was used to select the optimal model using  the smallest value.
## The final value used for the model was mtry = 22.
```

```r
rdForestfit$finalModel
```

```
##
## Call:
##  randomForest(x = x, y = y, mtry = param$mtry, importance = TRUE,      verbose = TRUE, predictionBounds = ..2)
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 22
##
##           Mean of squared residuals: 213.0706
##                     % Var explained: 91.19
```

```r
pred <- predict(rdForestfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$new_customer_per_day_pass_7days, pred), order.by = dt_complete$date_parsed)
autoplot(dt_xts, facet=FALSE, main="[Random Forest] Real vs. Prediction: New 7-day pass")
```

{% asset_img unnamed-chunk-6-4.png %}

```r
# varImp(rdForestfit$finalModel)
# save mod in rds
# saveRDS(rdForestfit, "input/mod/MOD_rf_pass7d_new_user.rds")
```

## Number of Trips

In the [post](http://jinyi.me/2015/04/NYC-CitiBike-3-More-Data-Exploration.htm#Data_transforming), we have created new feature: `active membership`.

With the information of new subscriber and pass user, we can use the prediction to create the feature `active membership` for the targeted time.

So we firstly begin data combination:

### Combining data

This part we are going to use the previously created SQLite database to query the data. We only keep the data at daily level.


```r
# get sqlite database
db_bike <- src_sqlite("../input/rds/dt_bike_clean.sqlite3")
# get dt_bike and get year ==2014
dt_bike_sql <- tbl(db_bike, from="dt_bike") %>% filter(year==2014)
explain(dt_bike_sql)
dt_bike <- collect(dt_bike_sql)
# construct df by day
dt_day <- dt_bike %>% group_by(year, month, mday, usertype) %>%
    summarise(trip_count=n()) %>%
    mutate(datetime = ymd(paste0(year,"-",month,"-",mday))) %>%
    ungroup() %>%
    tidyr::spread(usertype, trip_count) %>%
    mutate(wday=wday(datetime), sum_count=Customer + Subscriber)
# remove dt_bike
rm(dt_bike)
# create season
dt_day$season <- NULL
dt_day$season[dt_day$month %in% c(9,10,11)] <- "fall"
dt_day$season[dt_day$month %in% c(12,1,2)] <- "winter"
dt_day$season[dt_day$month %in% c(3,4,5)] <- "spring"
dt_day$season[dt_day$month %in% c(6,7,8)] <- "summer"
# get weather
dt_weather_aggr <- readRDS("../input/rds/dt_knyc_2014_aggr.rds")
dt_weather_aggr$key <- as.character(floor_date(dt_weather_aggr$Date, unit="hour"))
dt_weather_aggr <- dt_weather_aggr[(!duplicated(dt_weather_aggr$key)),]
dt_weather_aggr$Precipitationmm <- as.numeric(dt_weather_aggr$Precipitationmm)
dt_weather_aggr$Precipitationmm[is.na(dt_weather_aggr$Precipitationmm)] <- 0
dt_join <- bind_cols(dt_day, dt_weather_aggr[,c("key","Mean_TemperatureC", "CloudCover", "Min_TemperatureC", "Max_TemperatureC", "Precipitationmm")])
dt_join$CloudCover[is.na(dt_join$CloudCover)] <- 0
dt_join <- tbl_df(dt_join)
# Get in active subscriber/customer information
dt_aggr <- readRDS("../input/rds/dt_aggr.rds")
dt_join <- dt_join %>%
  bind_cols(dt_aggr[,c("subscriber_active",
                       "customer_pass_active",
                       "new_subscriber_per_day",
                       "new_customer_per_day_pass_1day",
                       "new_customer_per_day_pass_7days")])
#GET public holiday
dt_holiday <- readRDS("../input/rds/dt_holiday_2014.rds")
dt_join <- bind_cols(dt_join, dt_holiday["holiday"])
```

Now prepare the data frame for modeling.


```r
# complete cases
dt_complete <- dt_join[complete.cases(dt_join),]
# transform
dt_complete$season <- as.factor(dt_complete$season)
dt_complete$month <- as.factor(dt_complete$month)
dt_complete$wday <- as.factor(dt_complete$wday)
dt_complete$mday <- as.factor(dt_complete$mday)

dt_complete$holiday <- as.factor(dt_complete$holiday)
```

### Correlation exploration

Now let's visualize the correlation between the count of trips and the predictors. Here plots trips carried out by pass users (customer), annual subscriber and sum of them.


```r
pairs(~sum_count+(month)+
    (season)+(wday)+(CloudCover)+(Max_TemperatureC)+
    (Mean_TemperatureC) + Precipitationmm+I(subscriber_active+customer_pass_active) + holiday
     , data=dt_complete, main="# of trips vs predictors")
```

{% asset_img unnamed-chunk-9-1.png %}

```r
pairs(~Customer+(month)+
    (season)+(wday)+(CloudCover)+(Max_TemperatureC)+
    (Mean_TemperatureC) + Precipitationmm+customer_pass_active + holiday
     , data=dt_complete, main="# of pass users' trips vs predictors")
```

{% asset_img unnamed-chunk-9-2.png %}

```r
pairs(~Subscriber+(month)+
    (season)+(wday)+(CloudCover)+(Max_TemperatureC)+
    (Mean_TemperatureC) + Precipitationmm +subscriber_active + holiday
    , data=dt_complete, main="# of subscribers' trips vs predictors")
```

{% asset_img unnamed-chunk-9-3.png %}

From the plots, we can see some clear correlation between the predictor and outcome variable. As observed in the plot, the following variables will be in the model: month, season, day of the week, Cloud Cover, max temperature,  average temperature, holiday, precipitation and active membership.

### Modeling

We need to create the model for annual subscriber trips & customer trips, but it is possible to only create the model for the total trips.


```r
# Annual Subscriber -----------------------------------
## model GAM
fitControl <- trainControl(method = "timeslice",
    initialWindow=120,
    horizon =30,
    fixedWindow =FALSE,
    predictionBounds=c(min(dt_complete$Subscriber), NA))
gamfit <- train(Subscriber ~ holiday + season+(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC) + Precipitationmm ,
                 data = dt_complete,
                 method = "gam",
                 trControl = fitControl,
                 verbose = TRUE)
gamfit
```

```
## Generalized Additive Model using Splines
##
## 365 samples
##  20 predictors
##
## No pre-processing
## Resampling: Rolling Forecasting Origin Resampling (30 held-out with no fixed window)
##
## Summary of sample sizes: 120, 121, 122, 123, 124, 125, ...
##
## Resampling results across tuning parameters:
##
##   select  RMSE      Rsquared   RMSE SD   Rsquared SD
##   FALSE   4096.975  0.6376181  1483.167  0.1968563
##    TRUE   4145.509  0.6352365  1548.066  0.1973177
##
## Tuning parameter 'method' was held constant at a value of GCV.Cp
## RMSE was used to select the optimal model using  the smallest value.
## The final values used for the model were select = FALSE and method
##  = GCV.Cp.
```

```r
summary(gamfit$final)
```

```
##
## Family: gaussian
## Link function: identity
##
## Formula:
## .outcome ~ seasonspring + seasonsummer + seasonwinter + wday2 +
##     wday3 + wday4 + wday5 + wday6 + wday7 + CloudCover + s(Mean_TemperatureC) +
##     s(Max_TemperatureC) + s(Precipitationmm)
##
## Parametric coefficients:
##              Estimate Std. Error t value Pr(>|t|)
## (Intercept)  16283.47     614.23  26.510  < 2e-16 ***
## seasonspring -1403.93     505.44  -2.778  0.00578 **
## seasonsummer  -812.06     645.04  -1.259  0.20892
## seasonwinter -3253.02     633.78  -5.133 4.82e-07 ***
## wday2         6398.66     632.88  10.110  < 2e-16 ***
## wday3         7701.23     642.19  11.992  < 2e-16 ***
## wday4         8478.56     634.29  13.367  < 2e-16 ***
## wday5         8407.93     630.79  13.329  < 2e-16 ***
## wday6         7228.45     626.07  11.546  < 2e-16 ***
## wday7          420.44     630.73   0.667  0.50549
## CloudCover    -144.89      86.97  -1.666  0.09666 .
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Approximate significance of smooth terms:
##                        edf Ref.df      F p-value
## s(Mean_TemperatureC) 2.181  2.826  2.868  0.0401 *
## s(Max_TemperatureC)  6.083  7.185  2.775  0.0076 **
## s(Precipitationmm)   7.364  8.269 22.360  <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## R-sq.(adj) =  0.883   Deviance explained = 89.2%
## GCV = 1.0617e+07  Scale est. = 9.8427e+06  n = 365
```

```r
# varImp(gamfit)
pred <- predict(gamfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete["Subscriber"], pred), order.by = dt_complete$datetime)
autoplot(dt_xts, facet=FALSE, main="[GAM] Real vs. Predicted: # Trips by Subscriber")
```

{% asset_img unnamed-chunk-10-1.png %}

```r
# saveRDS(gamfit, "input/mod/MOD_gam_subs_trip.rds")

## model randomForest
fitControl <- trainControl(method = "oob",
    predictionBounds=c(min(dt_complete$Subscriber), NA))
rdForestfit <- train(Subscriber ~ season:(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+Precipitationmm +holiday,
                 data = dt_complete,
                 method = "rf",
                 trControl = fitControl,
                 verbose = TRUE,
                 importance=TRUE)
rdForestfit
```

```
## Random Forest
##
## 365 samples
##  20 predictors
##
## No pre-processing
## Resampling results across tuning parameters:
##
##   mtry  RMSE      Rsquared
##    2    4565.770  0.7523463
##   17    3477.116  0.8563667
##   33    3514.528  0.8532592
##
## RMSE was used to select the optimal model using  the smallest value.
## The final value used for the model was mtry = 17.
```

```r
rdForestfit$finalModel
```

```
##
## Call:
##  randomForest(x = x, y = y, mtry = param$mtry, importance = TRUE,      verbose = TRUE)
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 17
##
##           Mean of squared residuals: 12257169
##                     % Var explained: 85.44
```

```r
# varImp(rdForestfit)
pred <- predict(rdForestfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$Subscriber, pred), order.by = dt_complete$datetime)
autoplot(dt_xts, facet=FALSE, main="[Random Forest] Real vs. Predicted: # Trips by Subscriber")
```

{% asset_img unnamed-chunk-10-2.png %}

```r
# saveRDS(rdForestfit, "input/mod/MOD_rf_subs_trip.rds")

# Annual Subscriber -----------------------------------
## model GAM
fitControl <- trainControl(method = "timeslice",
    initialWindow=120,
    horizon =30,
    fixedWindow =FALSE,
    predictionBounds=c(min(dt_complete$Customer), NA))
gamfit <- train(Customer ~ season+(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC) + Precipitationmm + customer_pass_active+holiday,
                 data = dt_complete,
                 method = "gam",
                 trControl = fitControl,
                 verbose = TRUE)
gamfit
```

```
## Generalized Additive Model using Splines
##
## 365 samples
##  20 predictors
##
## No pre-processing
## Resampling: Rolling Forecasting Origin Resampling (30 held-out with no fixed window)
##
## Summary of sample sizes: 120, 121, 122, 123, 124, 125, ...
##
## Resampling results across tuning parameters:
##
##   select  RMSE      Rsquared   RMSE SD   Rsquared SD
##   FALSE   290.4210  0.9693042  129.2642  0.02952503
##    TRUE   295.2867  0.9686040  134.1195  0.03011518
##
## Tuning parameter 'method' was held constant at a value of GCV.Cp
## RMSE was used to select the optimal model using  the smallest value.
## The final values used for the model were select = FALSE and method
##  = GCV.Cp.
```

```r
summary(gamfit$final)
```

```
##
## Family: gaussian
## Link function: identity
##
## Formula:
## .outcome ~ seasonspring + seasonsummer + seasonwinter + wday2 +
##     wday3 + wday4 + wday5 + wday6 + wday7 + CloudCover + s(Mean_TemperatureC) +
##     s(Max_TemperatureC) + s(Precipitationmm) + s(customer_pass_active)
##
## Parametric coefficients:
##              Estimate Std. Error t value Pr(>|t|)
## (Intercept)  2399.075     40.099  59.828  < 2e-16 ***
## seasonspring  -41.461     30.383  -1.365  0.17327
## seasonsummer -121.577     37.928  -3.205  0.00148 **
## seasonwinter   45.386     40.481   1.121  0.26301
## wday2         -67.837     42.657  -1.590  0.11269
## wday3        -216.016     44.276  -4.879 1.64e-06 ***
## wday4        -201.795     43.215  -4.670 4.35e-06 ***
## wday5        -225.962     42.124  -5.364 1.50e-07 ***
## wday6        -199.386     40.253  -4.953 1.15e-06 ***
## wday7        -155.950     37.475  -4.161 4.01e-05 ***
## CloudCover    -12.773      5.137  -2.486  0.01339 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Approximate significance of smooth terms:
##                           edf Ref.df        F  p-value
## s(Mean_TemperatureC)    2.794  3.580    1.108    0.345
## s(Max_TemperatureC)     1.000  1.000    0.326    0.568
## s(Precipitationmm)      3.590  4.331    8.666 6.16e-07 ***
## s(customer_pass_active) 5.971  7.151 1206.232  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## R-sq.(adj) =  0.992   Deviance explained = 99.2%
## GCV =  37085  Scale est. = 34610     n = 365
```

```r
# varImp(gamfit)
pred <- predict(gamfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$Customer, pred), order.by = dt_complete$datetime)
autoplot(dt_xts, facet=FALSE, main="[GAM] Real vs. Predicted: # Trips by Customer")
```

{% asset_img unnamed-chunk-10-3.png %}

```r
# saveRDS(gamfit, "input/mod/MOD_gam_cust_trip.rds")

## model randomForest
fitControl <- trainControl(method = "oob",
    predictionBounds=c(min(dt_complete$Customer), NA))
rdForestfit <- train(Customer ~ season+(wday)+(CloudCover)+(Max_TemperatureC)+ (Mean_TemperatureC)+Precipitationmm + customer_pass_active + holiday,
                 data = dt_complete,
                 method = "rf",
                 trControl = fitControl,
                 verbose = TRUE,
                 importance=TRUE)
rdForestfit
```

```
## Random Forest
##
## 365 samples
##  20 predictors
##
## No pre-processing
## Resampling results across tuning parameters:
##
##   mtry  RMSE      Rsquared
##    2    572.2227  0.9217813
##    8    262.7175  0.9835124
##   15    237.5122  0.9865243
##
## RMSE was used to select the optimal model using  the smallest value.
## The final value used for the model was mtry = 15.
```

```r
rdForestfit$finalModel
```

```
##
## Call:
##  randomForest(x = x, y = y, mtry = param$mtry, importance = TRUE,      verbose = TRUE)
##                Type of random forest: regression
##                      Number of trees: 500
## No. of variables tried at each split: 15
##
##           Mean of squared residuals: 53958.1
##                     % Var explained: 98.71
```

```r
# varImp(rdForestfit)
pred <- predict(rdForestfit, newdata=dt_complete)
dt_xts <- xts(cbind(dt_complete$Customer, pred), order.by = dt_complete$datetime)
autoplot(dt_xts, facet=FALSE, main="[Random Forest] Real vs. Predicted: # Trips by Customer")
```

{% asset_img unnamed-chunk-10-4.png %}

```r
# saveRDS(rdForestfit, "input/mod/MOD_rf_cust_trip.rds")
```

## Summary

In this post, we have constructed the model with random forest and general addictive model and done some feature engineering to create some useful features: active membership, holiday, day of week, etc...

With the current model (having around 80% - 90% accuracy), we can begin the construction of [shiny](http://shiny.rstudio.com) application.
