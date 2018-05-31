---
title: "Reproducible"
author: "Qasim Sheikh"
date: "May 31, 2018"
output: html_document
---

##Getting and organizing the data
**1. Getting the Data**

```r
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(url, "file")
unzip("file")
activitydata <- read.csv("activity.csv")
```

**2. Organizing the data to remove NAs and have dates in date format**

```r
activitydata$date = as.Date(activitydata$date)
activitydata2 <- na.omit(activitydata)
```

##Mean total number of steps per day 
**1. Total number of steps per day**

```r
stepsperday <- as.matrix(tapply(activitydata2$steps, activitydata2$date, sum))
stepsperday
```

```
##             [,1]
## 2012-10-02   126
## 2012-10-03 11352
## 2012-10-04 12116
## 2012-10-05 13294
## 2012-10-06 15420
## 2012-10-07 11015
## 2012-10-09 12811
## 2012-10-10  9900
## 2012-10-11 10304
## 2012-10-12 17382
## 2012-10-13 12426
## 2012-10-14 15098
## 2012-10-15 10139
## 2012-10-16 15084
## 2012-10-17 13452
## 2012-10-18 10056
## 2012-10-19 11829
## 2012-10-20 10395
## 2012-10-21  8821
## 2012-10-22 13460
## 2012-10-23  8918
## 2012-10-24  8355
## 2012-10-25  2492
## 2012-10-26  6778
## 2012-10-27 10119
## 2012-10-28 11458
## 2012-10-29  5018
## 2012-10-30  9819
## 2012-10-31 15414
## 2012-11-02 10600
## 2012-11-03 10571
## 2012-11-05 10439
## 2012-11-06  8334
## 2012-11-07 12883
## 2012-11-08  3219
## 2012-11-11 12608
## 2012-11-12 10765
## 2012-11-13  7336
## 2012-11-15    41
## 2012-11-16  5441
## 2012-11-17 14339
## 2012-11-18 15110
## 2012-11-19  8841
## 2012-11-20  4472
## 2012-11-21 12787
## 2012-11-22 20427
## 2012-11-23 21194
## 2012-11-24 14478
## 2012-11-25 11834
## 2012-11-26 11162
## 2012-11-27 13646
## 2012-11-28 10183
## 2012-11-29  7047
```

**2. A histogram of the total number of steps per day** 

```r
hist(stepsperday)
```

<img src="PA1_template_files/figure-html/unnamed-chunk-4-1.png" width="672" />

**3. The mean and median of the total number of steps per day** 

```r
mean(stepsperday)
```

```
## [1] 10766.19
```

```r
median(stepsperday)
```

```
## [1] 10765
```

##Average daily activity pattern 
**1. Plotting steps across intervals**

```r
stepsperint <- tapply(activitydata2$steps, activitydata2$interval, mean)
plot(unique(activitydata2$interval), stepsperint, type = "l", xlab = "interval", ylab = "steps")
```

<img src="PA1_template_files/figure-html/unnamed-chunk-6-1.png" width="672" />

**2. Five minute interval with maximum number of steps**

```r
stepsperint[stepsperint==max(stepsperint)]
```

```
##      835 
## 206.1698
```

835 is the interval with the highest steps per day
##Imputing missing values
**1. Reporting the total number of missing observations in the dataset**

```r
nrow(activitydata) - nrow(activitydata2)
```

```
## [1] 2304
```

**2. Filling in the missing values with the mean by interval** 

```r
library(Hmisc)
activitydata3 = activitydata
activitydata3$steps = impute(activitydata3$steps, fun = mean)
```

**3. Histogram and statistics of new dataset**

```r
stepsperday1 <- as.matrix(tapply(activitydata3$steps, activitydata3$date, sum))
mean(stepsperday1)
```

```
## [1] 10766.19
```

```r
median(stepsperday1)
```

```
## [1] 10766.19
```

```r
hist(stepsperday1)
```

<img src="PA1_template_files/figure-html/unnamed-chunk-10-1.png" width="672" />

Given that the mean of the data was used to calculate the values to enter into the NA
column, the mean and median of the new data are not different from the mean and median
of the previous data. This would not be the case if mean and median by interval or date
had been done. 

##Weekdays and weekends
**1. Creating the new factor**

```r
wdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
activitydata3$wday <- factor((weekdays(activitydata3$date) %in% wdays), levels = c(TRUE, FALSE), 
                     labels = c("Weekday", "Weekend"))
```

**2. Plotting the data**

```r
activitydata4 <- aggregate(steps ~interval + wday, data = activitydata3, mean)
ggplot(activitydata4, aes(interval, steps)) + facet_grid(wday~.) + geom_line()
```

<img src="PA1_template_files/figure-html/unnamed-chunk-12-1.png" width="672" />
