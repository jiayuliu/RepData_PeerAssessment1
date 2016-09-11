Reproducible Research Course Project
========================================================

##Loading and preprocessing the data

Load the data 


```r
setwd("/Users/liujiayu/Desktop/coursera_data science/Reproducible Research")
data <- read.csv("activity.csv")
```


##What is mean total number of steps taken per day?

Make a histogram of the total number of steps taken each day


```r
stepsPerDay <- aggregate(steps ~ date, data, sum)
hist(stepsPerDay$steps, main = "Steps per day", xlab = "Steps", col = "blue", breaks = 8)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

Calculate and report the mean and median total number of steps taken per day


```r
meanStepsPerDay <- mean(stepsPerDay$steps)
medianStepsPerDay <- median(stepsPerDay$steps)
```

Mean is 10766.19 and median is 10765.


##What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
stepsInterval <- aggregate(steps ~ interval, data, mean)
plot(stepsInterval$interval, stepsInterval$steps, type="l", xlab = "5 min - interval", ylab = "Average steps", main = "Average Daily Activity Pattern", col = "blue")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
stepsInterval$interval[which.max(stepsInterval$steps)]
```

```
## [1] 835
```

##Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
nrow(data[is.na(data$steps),])
```

```
## [1] 2304
```


Create a new dataset that is equal to the original dataset but with the missing data filled in.

Replace NA with 0


```r
dataWithoutNAs <- data
dataWithoutNAs[is.na(dataWithoutNAs$steps), "steps"] <- 0
```


Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
stepsPerDayNoNAs <- aggregate(steps ~ date, dataWithoutNAs, sum)
hist(stepsPerDayNoNAs$steps, main = "Steps per day", xlab = "Steps", col = "blue", breaks = 8)
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 



```r
meanStepsPerDayNoNAs <- mean(stepsPerDayNoNAs$steps)
medianStepsPerDayNoNAs <- median(stepsPerDayNoNAs$steps)
```

Mean is 9354.23 and median is 10395.

Because NAs are replaced with 0, the mean and median shift left.


##Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
dataWithoutNAs$day <- as.POSIXlt(dataWithoutNAs$date)$wday
dataWithoutNAs$dayType <- as.factor(ifelse(dataWithoutNAs$day == 0 | dataWithoutNAs$day == 6, "weekend", "weekday"))
dataWithoutNAs <- subset(dataWithoutNAs, select = -c(day))
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using simulated data:


```r
weekdaysData <- dataWithoutNAs[dataWithoutNAs$dayType == "weekday",]
weekendsData <- dataWithoutNAs[dataWithoutNAs$dayType == "weekend",]
stepsIntervalWeekdays <- aggregate(steps ~ interval, weekdaysData, mean)
stepsIntervalWeekends <- aggregate(steps ~ interval, weekendsData, mean)

par(mfrow = c(2, 1))

plot(stepsIntervalWeekdays, type = "l", col = "green", main = "Weekdays")
plot(stepsIntervalWeekends, type = "l", col = "red", main = "Weekends")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png) 

Based on the plots, the activity paths at the weekends are different from those at the weekdays. The distribution at the weekends uniform while the distribution at the weekdays has a clear peak in the morning.



