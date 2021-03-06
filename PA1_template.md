Loading and preprocessing the data
=======================================

Data is downloaded from 
 https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip
 9 January 2016


```r
activity <- read.csv("C:/Users/mbonifac/Desktop/Coursera/R_Portfolio/activity.csv")
```

What is the mean total number of steps taken per day?
=======================================

Create a data set summing steps recorded each day, ignore NA cases


```r
activity2 <- activity[complete.cases(activity),]
dayactivity <- aggregate(activity2$steps, by=list(activity2$date), FUN = "sum")
colnames(dayactivity) <- c("date", "steps")
```

Plot histogram of total steps taken in a day


```r
hist(dayactivity$steps, col = "red", main = paste("Histogram of", "Total Steps Taken per Day"), xlab = "Total Steps Taken in a Day")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)

Create a data frame listing the number of steps recorded each day


```r
meanactivity <- aggregate(activity2$steps, by=list(activity2$date), FUN = "mean")
colnames(meanactivity) <- c("date", "mean_steps")
```

Create a data frame listing the median number of steps recorded each day


```r
medactivity <- aggregate(activity2$steps, by=list(activity2$date), FUN = "median")
colnames(medactivity) <- c("date", "median_steps")
```

What is the average daily activity pattern?
=======================================

Create a data frame listing average number of steps recorded at each interval, ignoring NA


```r
intactivity <- aggregate(activity2$steps, by=list(activity2$interval), FUN = "mean")
colnames(intactivity) <- c("interval", "mean_steps")
```

Plot the mean_steps vs interval


```r
plot(intactivity$interval, intactivity$mean_steps, type = "l", main = paste("Mean Steps per Time Interval, NAs Ignored"), ylab = "Mean Steps", xlab = "Time Interval")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

The maximum mean is 206 steps at the 835 interval

Imputing missing values
=======================================

First determine the number of missing values in activity data frame


```r
NA_count <- nrow(activity) - nrow(activity2)
NA_count
```

```
## [1] 2304
```

The NA values are replaced with average daily value
A histogram is made of the total steps taken each day


```r
activity3 <- activity
activity3$steps[which(is.na(activity3$steps))]<-mean(meanactivity$mean_steps)
## Create a data set summing steps per day with the substituted NA values used
dayactivity_sub <- aggregate(activity3$steps, by=list(activity3$date), FUN = "sum")
colnames(dayactivity_sub) <- c("date", "steps")
## Plot histogram of total steps taken in a day using the substituted values
hist(dayactivity_sub$steps, col = "blue", main = paste("Histogram of Total Steps Taken per Day, NA values substituted"), xlab = "Total Steps Taken in a Day")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png)

Compare median and mean of the data set with NAs removed (dayactivity)
to that of the dat set with replaced NAs (dayactivity_sub)


```r
dayactivity_mean<-mean(dayactivity$steps)
```


```r
dayactivity_median<-median(dayactivity$steps)
```


```r
dayactivity_sub_mean<-mean(dayactivity_sub$steps)
```


```r
dayactivity_sub_median<-median(dayactivity_sub$steps)
```

There is little variation between the mean and median values for both data sets

Are there differences in activity patterns between weekdays and weekends?
=======================================

Data set with the filled-in NA values (activity3) is used
The Date column is changed from factor to date
Day of the week is also added to data frame
Weekday/Weekend designation is added


```r
activity3$date<-as.Date(activity3$date)
## Add column identifying day of the week
day<-weekdays(activity3$date)
activity4<-cbind(activity3,day)
## Create factors identifying weekdays and weekends
day <-c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday")
wkday <- c("Weekday", "Weekday", "Weekday", "Weekday", "Weekday", "Weekend", "Weekend")
daycat<-cbind(day,wkday)
## Merge activity4 with daycat to apply weekday/weekend factors
activity4<-merge(activity4,daycat)
```

Panel plot containing the time series of the interval vs. average number of steps
across all weekdays and weekends


```r
## Create a data set averaging steps by interval and weekday/weekend
daycatact_sub <- aggregate(activity4$steps, by=list(activity4$interval, activity4$wkday), FUN = "mean")
colnames(daycatact_sub)<-c("interval", "wkday", "mean_steps")
library(lattice)
xyplot(mean_steps ~ interval | wkday, data = daycatact_sub, type = "l", layout = c(1, 2))
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png)

More steps appear to be taken in the morning on weekdays than on weekends
