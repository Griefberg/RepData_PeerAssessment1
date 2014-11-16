Reproducible Research: Peer Assessment 1
========================================
by Griefberg  
github repo for assignment: https://github.com/Griefberg/RepData_PeerAssessment1


```r
library(lubridate)
library(ggplot2)
Sys.setlocale("LC_TIME", "en_US")
```
## Loading and preprocessing the data 

```r
unzip('activity.zip')
data <- read.csv('activity.csv')
data$date <- ymd(data$date)
```

## What is mean total number of steps taken per day?  
- Make a histogram of the total number of steps taken each day

```r
steps <- aggregate(steps ~ date, data = data, sum)
ggplot(data = steps, aes(date, steps)) + geom_bar(stat = 'identity') + ylab('Number of steps')
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 
- Calculate and report the mean and median total number of steps taken per day

```r
round(mean(steps$steps), digits = 0)
```

```
## [1] 10766
```

```r
round(median(steps$steps), digits = 0)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
- Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
interval <- aggregate(steps ~ interval, data = data, mean)
ggplot(data = interval, aes(interval, steps)) + geom_line() + ylab('Number of steps')
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 
- Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
interval$interval[which.max(interval$steps)]
```

```
## [1] 835
```
## Imputing missing values  
- Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs) 

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```
- Filling in all of the missing values in the dataset (NA = mean of that 5-minute interval)  

```r
names(interval)[2] <- 'steps.interval'
data <- merge(data, interval, by = 'interval')
for (i in 1:nrow(data)){
        if (is.na(data$steps[i])){
                data$steps[i] <- data$steps.interval[i]
        }
}
data$steps.interval <- NULL
```
- Make a histogram of the total number of steps taken each day. Calculate the mean and median total number of steps taken per day. 

```r
steps2 <- aggregate(steps ~ date, data = data, sum)
ggplot(data = steps2, aes(date, steps)) + geom_bar(stat = 'identity') + ylab('Number of steps')
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 

```r
round(mean(steps2$steps), digits = 0)
```

```
## [1] 10766
```

```r
round(median(steps2$steps), digits = 0)
```

```
## [1] 10766
```
  
As we can see, these values almost don't differ from the earlier estimates. Filling in all of the missings with the means have led to equality of the mean and median total number of steps taken per day.  

## Are there differences in activity patterns between weekdays and weekends?  
- Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.  

```r
data$weekday <- 'weekday'
data$weekday[weekdays(data$date) %in% c('Sunday', 'Saturday')] <- 'weekend'
data$weekday <- as.factor(data$weekday)
```
- Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).  

```r
interval <- aggregate(steps ~ interval + weekday, data = data, mean)
ggplot(data = interval, aes(interval, steps)) + geom_line() + facet_wrap( ~ weekday, ncol=1) + ylab('Number of steps')
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png) 
