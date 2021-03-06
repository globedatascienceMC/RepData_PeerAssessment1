---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---
## Loading and preprocessing the data

```r
act <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?
1. Calculate the total number of steps taken per day

```r
library(dplyr)
act.total.steps <- act %>% 
  group_by(date) %>%
  summarize(total.steps = sum(steps))
```

2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
hist(act.total.steps$total.steps, main="Histogram of Steps", xlab="Total Steps")
```

![](PA1_template_files/figure-html/hist.steps-1.png)<!-- -->

3. Calculate and report the mean and median of the total number of steps taken per day

```r
mean(act.total.steps$total.steps, na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(act.total.steps$total.steps, na.rm= TRUE)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
#get the average per day
library(dplyr)
act.ave.steps <- act %>% 
  group_by(interval) %>%
  summarize(average.steps = mean(steps, na.rm = TRUE))
#time series plot
plot(act.ave.steps$interval, act.ave.steps$average.steps, type="l", xaxt='n', 
     xlab="Interval", ylab="Ave Number of Steps", main="Interval by Average Daily Steps")
```

![](PA1_template_files/figure-html/timeseries-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
max.steps <- max(act.ave.steps$average.steps)
act.ave.steps[act.ave.steps$average.steps==max.steps,]
```

```
## # A tibble: 1 x 2
##   interval average.steps
##      <int>         <dbl>
## 1      835          206.
```

## Inputting missing values
Note that there are a number of days/intervals where there are missing values NA. The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs

```r
sum(is.na(act))
```

```
## [1] 2304
```
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
act2 <- read.csv("activity.csv")
act2$steps <- ifelse(is.na(act2$steps), mean(act2$steps, na.rm=TRUE), act2$steps)
```
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
write.csv(act2, file = "Activity without missing.csv")
```
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
#get total steps
act2.total.steps <- act2 %>% 
  group_by(date) %>%
  summarize(total.steps = sum(steps))
#plot total steps
hist(act2.total.steps$total.steps, main="Histogram of Steps without Missing Data", xlab="Total Steps")
```

![](PA1_template_files/figure-html/act2-1.png)<!-- -->

```r
#get mean and median
mean(act2.total.steps$total.steps)
```

```
## [1] 10766.19
```

```r
median(act2.total.steps$total.steps)
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?
1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
act2$dayofweek <- weekdays(as.Date(act2$date))
act2$weekend <-as.factor(act2$dayofweek=="Saturday"|act2$dayofweek=="Sunday")
levels(act2$weekend) <- c("Weekday", "Weekend")
```
2. Make a panel plot containing a time series plot (i.e. type=l) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
#get average of weekend and weekday
act.week.day <- act2 %>% 
  group_by(weekend, interval) %>%
  summarize(average.steps = mean(steps, na.rm = TRUE))
#create plot
library(ggplot2)
ggplot(act.week.day, aes(interval, average.steps)) + geom_line() + facet_grid(weekend ~ .) +
labs(x="Intervals", y="Average Steps", title="Average Steps, Weekend vs Weekday") + 
  theme(plot.title = element_text(hjust = 0.5))
```

![](PA1_template_files/figure-html/ave.weekday-1.png)<!-- -->
