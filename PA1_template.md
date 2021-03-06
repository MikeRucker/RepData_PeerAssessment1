# Reproducible Research: Peer Assessment 1

Work by: Mike Rucker, Ph.D.
Date: 7/16/2017

## Loading and preprocessing the data

1. Code (and libraries) for reading in the dataset and/or processing the data

```r
library(knitr)
library(plyr)
library(ggplot2)
library(scales)

activity <- read.csv("C:/temp/activity.csv", header=TRUE, sep=",")
```

## What is mean total number of steps taken per day?

2. Histogram of the total number of steps taken each day

```r
hist(StepsPerDay, xlab = "Number of Steps", main = "Histogram: Steps per Day")
```

![](C:/figure/hist-steps-per-day.png)

3. Mean and median number of steps taken each day

```r
MeanPerDay <- mean(StepsPerDay, na.rm = TRUE)
MedianPerDay <- median(StepsPerDay, na.rm = TRUE)
```

The mean per day is: `10766.1886792453`
The median per day: `10765L` 

## What is the average daily activity pattern?

4. Time series plot of the average number of steps taken

```r
StepsPerInterval <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)
plot(as.numeric(names(StepsPerInterval)), 
     StepsPerInterval, 
     xlab = "Interval", 
     ylab = "Steps", 
     main = "Average Daily Activity Pattern", 
     type = "l")
```

![](C:/figure/average-daily-activty-pattern.png)

5. The 5-minute interval that, on average, contains the maximum number of steps

```r
MaxInterval <- names(sort(StepsPerInterval, decreasing = TRUE)[1])
MaxSteps <- sort(StepsPerInterval, decreasing = TRUE)[1]
```

The 5-minute interval that, on average, contains the maximum number of steps is: `MaxInterval`

## Imputing missing values

6. Code to describe and show a strategy for imputing missing data
*The code below splits the available activity data by interval, then fills in each interval that has missing data (using the dataset mean)*

```r
StepsPerInterval <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)
activity.split <- split(activity, activity$interval)
for(i in 1:length(activity.split)){
    activity.split[[i]]$steps[is.na(activity.split[[i]]$steps)] <- StepsPerInterval[i]
}
activity.imputed <- do.call("rbind", activity.split)
activity.imputed <- activity.imputed[order(activity.imputed$date) ,]
```

7. Histogram of the total number of steps taken each day after missing values are imputed

```r
StepsPerDay.imputed <- tapply(activity.imputed$steps, activity.imputed$date, sum)
hist(StepsPerDay.imputed, xlab = "Number of Steps", main = "Histogram: Steps per Day (Imputed data)")
```

![](C:/figure/steps-per-day_imputed-data.png)

8. Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

```r
MeanPerDay.imputed <- mean(StepsPerDay.imputed, na.rm = TRUE)
MedianPerDay.imputed <- median(StepsPerDay.imputed, na.rm = TRUE)
activity.imputed$day <- ifelse(weekdays(as.Date(activity.imputed$date)) == "Saturday" | weekdays(as.Date(activity.imputed$date)) == "Sunday", "weekend", "weekday")
StepsPerInterval.weekend <- tapply(activity.imputed[activity.imputed$day == "weekend" ,]$steps, activity.imputed[activity.imputed$day == "weekend" ,]$interval, mean, na.rm = TRUE)
StepsPerInterval.weekday <- tapply(activity.imputed[activity.imputed$day == "weekday" ,]$steps, activity.imputed[activity.imputed$day == "weekday" ,]$interval, mean, na.rm = TRUE)
par(mfrow=c(1,2))
plot(as.numeric(names(StepsPerInterval.weekday)), 
     StepsPerInterval.weekday, 
     xlab = "Interval", 
     ylab = "Steps", 
     main = "Activity Pattern (Weekdays)", 
     type = "l")
plot(as.numeric(names(StepsPerInterval.weekend)), 
     StepsPerInterval.weekend, 
     xlab = "Interval", 
     ylab = "Steps", 
     main = "Activity Pattern (Weekends)", 
     type = "l")
```
![](C:/figure/panel-plot.png)

9. All of the R code needed to reproduce the results (numbers, plots, etc.) in the report

I have put all code, including output of numbers and plots, in the above report.
