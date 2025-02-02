---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

```r
df <- read.csv("activity.csv")
df$date <- as.Date(df$date)
```

## What is mean total number of steps taken per day?

```r
total_num <- aggregate(df$steps, by=list(df$date), FUN = sum)
hist(total_num$x)
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
mean <- as.numeric(summary(total_num$x)["Mean"])
median <- as.numeric(summary(total_num$x)["Median"])
```

The total number of steps taken per day is `total_num`.

The mean and median of the total number of steps taken per day is `mean` and `median`, respectively.

## What is the average daily activity pattern?

```r
avg_num <- aggregate(df$steps, by=list(df$interval), FUN = mean, na.rm = TRUE)
plot(avg_num$Group.1,avg_num$x,type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

```r
inter_maxstep <- avg_num$Group.1[avg_num$x == max(avg_num$x)]
```

The interval `inter_maxstep` contains the maximum number of steps.

## Imputing missing values

```r
miss_num <- sum(is.na(df$steps))

df_filled <- df
for (i in 1:nrow(df_filled)) {
    if (is.na(df_filled[i,]$steps)){
        df_filled[i,]$steps <- avg_num$x[avg_num$Group.1 == df_filled[i,]$interval]
    }
}

total_num_filled <- aggregate(df_filled$steps, by=list(df_filled$date), FUN = sum)
hist(total_num_filled$x)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
mean_filled <- as.numeric(summary(total_num_filled$x)["Mean"])
median_filled <- as.numeric(summary(total_num_filled$x)["Median"])
```

The total number of missing values is `miss_num`.

The mean and median of the total number of steps taken per day after missing data filled is `mean_filled` and `median_filled`, respectively. The values are quite similar with the original values. Imputing missing data change the distribution of total daily number of steps, especially 1st Qu and 3st Qu.

## Are there differences in activity patterns between weekdays and weekends?

```r
df_filled$weekdays <- weekdays(df_filled$date)
df_filled$weekday <- ifelse(df_filled$weekdays %in% c("星期六", "星期日"), "weekend","weekday")

avg_num_weekday <- aggregate(df_filled$steps[df_filled$weekday == "weekday"], by=list(df_filled$interval[df_filled$weekday == "weekday"]), FUN = mean)
avg_num_weekend <- aggregate(df_filled$steps[df_filled$weekday == "weekend"], by=list(df_filled$interval[df_filled$weekday == "weekend"]), FUN = mean)

par(mfrow = c(1,2))
plot(avg_num_weekday$Group.1,avg_num$x,type = "l")
plot(avg_num_weekend$Group.1,avg_num$x,type = "l")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

