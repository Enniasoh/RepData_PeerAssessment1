---
title: "RMD"
author: "ME!"
date: "12/21/2021"
output: html_document
---


```reading
originalData <- read.csv("activity.csv",header=TRUE)
datalessNA <- na.omit(originalData)
```

## 1. What is mean total number of steps taken per day?

## 1A. Calculate the total number of steps taken per day



```total
steps_each_day <- aggregate(datalessNA$steps, by= list(datalessNA$date), FUN="sum")
```

## 1B. Make a histogram of the total number of steps taken each day


```histogram
hist(steps_each_day$x, col="blue", xlab = "Number of steps per day",
     main = "Total number of steps taken each day", breaks = 30)
```

## 1C. Calculate and report the mean and median of the total number of steps taken per day


```mean
mean_steps <- mean(steps_each_day[,2])
```


```median_steps
median_steps <- median(steps_each_day[,2])
```

## 2. What is the average daily activity pattern?

## 2A. Make a time series plot (i.e. \color{red}{\verb|type = "l"|}type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```mean_each_day
mean_each_day <- aggregate(datalessNA$steps, by = list(Interval = datalessNA$interval), FUN = "mean")
```


```plot
png("timeseries.png")
plot(mean_each_day$Interval, mean_each_day$x, type = "l", xlab = "5-min intervals",
     ylab = "Average number of steps taken", main = " Average daily activity pattern")
dev.off()

```

## 2B. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```interval
interval_row <- which.max(mean_each_day$x)
max_interval <- mean_each_day[interval_row, 1]
```

## 3. Note that there are a number of days/intervals where there are missing values (coded as red NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

## 3A. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```number
number_of_NAs <- length(which(is.na(originalData$steps)))
```

## 3B. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

## 3C.Create a new dataset that is equal to the original dataset but with the missing data filled in.


```fill
originalData_filled <- originalData 
originalData_filled$steps <- impute(originalData$steps, fun = mean)
```

## 3D. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```plot
steps_each_day_noNAs <- aggregate(originalData_filled$steps, by = list(originalData_filled$date), FUN="sum")
png("histogram2.png")
hist(steps_each_day_noNAs$x, col="green", xlab = "Number of steps per day",
     main = "Total number of steps taken each day", breaks = 30)
dev.off()
```


```mean
mean_steps_NAsfilled <- mean(steps_each_day_noNAs[,2])
```


```median_steps
median_steps_NAsfilled <- median(steps_each_day_noNAs[,2])
```

## 5. Are there differences in activity patterns between weekdays and weekends?

## 5A. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```create
originalData_filled$date <- as.Date(originalData_filled$date)
originalData_filled$weekday <- weekdays(originalData_filled$date)
originalData_filled$weekend <- ifelse(originalData_filled$weekday == "Saturday" | originalData_filled$weekday == "Sunday", "Weekend", "Weekday")
```

## 5B. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```create
day_type <- aggregate(steps ~ interval + weekend, data = originalData_filled, mean)
library(ggplot2)
png("timeseries2.png")
ggplot(day_type, aes(interval, steps)) + geom_line() + facet_grid(weekend ~ .) +
        xlab("5-minute intervals") + 
        ylab("Avarage number of steps taken") + 
        ggtitle ("Weekdays & Weekends activity pattern")
dev.off()
```


