Following is an illustration of R Markdown containing text and code
chunks.

Loading and preprocessing the data
----------------------------------

1.) Load the data (i.e. read.csv())

    data_file <- unzip("C://Users//pgnanesh.ORADEV//Reproducible_Research//activity.zip")
    data <- read.csv(data_file, stringsAsFactors = FALSE, na.strings = "NA")

2.) Process/transform the data (if necessary) into a format suitable for
your analysis

    data$date <- as.Date(data$date,format = "%Y-%m-%d")

What is mean total number of steps taken per day?
-------------------------------------------------

For this part of the assignment, you can ignore the missing values in
the dataset.

1.) Calculate the total number of steps taken per day

    total_steps_per_day <- aggregate(steps ~ date, data=data, FUN=sum)

2.) If you do not understand the difference between a histogram and a
barplot, research the difference between them. Make a histogram of the
total number of steps taken each day

    library(lattice)
    histogram(total_steps_per_day$steps, ylab="Frequency", xlab="Total no. of steps per day",
              breaks=seq(0,25000, by=500), xlim=c(0,25000))

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-4-1.png)

3.) Calculate and report the mean and median of the total number of
steps taken per day

    mean_steps <-mean(total_steps_per_day$steps)
    median_steps <- median(total_steps_per_day$steps)

1.076618910^{4} is the mean of total steps per day and 10765 is the
median of total steps per day.

What is the average daily activity pattern?
-------------------------------------------

1.)Make a time series plot (i.e. type = "l") of the 5-minute interval
(x-axis) and the average number of steps taken, averaged across all days
(y-axis)

    mean_steps_per_interval <- aggregate(steps~interval,data = data, FUN = mean)
    plot(mean_steps_per_interval, type="l",ylab="Average Steps taken", xlab="5-Minute Interval",
         xlim=c(0,2500),col="red")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

2.)Which 5-minute interval, on average across all the days in the
dataset, contains the maximum number of steps?

    mean_steps_per_interval$interval[which.max(mean_steps_per_interval$steps)]

    ## [1] 835

Imputing missing values
-----------------------

Note that there are a number of days/intervals where there are missing
values (coded as NA). The presence of missing days may introduce bias
into some calculations or summaries of the data.

1.) Calculate and report the total number of missing values in the
dataset (i.e. the total number of rows with NAs)

    sum(is.na(data))

    ## [1] 2304

2.) Devise a strategy for filling in all of the missing values in the
dataset. The strategy does not need to be sophisticated. For example,
you could use the mean/median for that day, or the mean for that
5-minute interval, etc. 3.) Create a new dataset that is equal to the
original dataset but with the missing data filled in.

    new_data <- data
    record <- 1
    for(day in 1:length(unique(data$date)) ) {
      for(interval in 1:288){
        if(is.na(data$steps[record])){
          new_data$steps[record] <- mean_steps_per_interval$steps[interval]
        }
        record <- record +1
      }
    }

4.) Make a histogram of the total number of steps taken each day

    new_total_steps_per_day <- aggregate(steps ~ date, data=new_data, FUN=sum)
    histogram(new_total_steps_per_day$steps,ylab = "Frequency", xlab="Total steps per day",
              breaks=seq(0,25000, by=500),xlim=c(0,25000))

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)

5.) Calculate and report the mean and median total number of steps taken
per day. Do these values differ from the estimates from the first part
of the assignment? What is the impact of imputing missing data on the
estimates of the total daily number of steps?

    mean(new_total_steps_per_day$steps)

    ## [1] 10766.19

    median(new_total_steps_per_day$steps)

    ## [1] 10766.19

    mean_diff <- mean(new_total_steps_per_day$steps) - mean(total_steps_per_day$steps)
    median_diff <- median(new_total_steps_per_day$steps) - median(total_steps_per_day$steps)

The new mean differs in 0 from the one calculated in the first part of
assignment and the median differs in 1.1886792 from the one calculated
in the first part of assignment.

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

For this part the weekdays() function may be of some help here. Use the
dataset with the filled-in missing values for this part.

1.) Create a new factor variable in the dataset with two levels -
"weekday" and "weekend" indicating whether a given date is a weekday or
weekend day.

    week_day <- c("Monday","Tuesday","Wednesday","Thursday","Friday")
    week <- ifelse(weekdays(new_data$date) %in% week_day,0,1)
    library(plyr)
    new_data <- mutate(new_data,day_of_week=week)
    new_data$day_of_week <- factor(new_data$day_of_week, labels=c("Weekday","Weekend"), 
                               levels=c(0,1))

2.) Make a panel plot containing a time series plot (i.e. type = "l") of
the 5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis). See the
README file in the GitHub repository to see an example of what this plot
should look like using simulated data.

    new_mean_steps_by_interval <- aggregate(steps~interval+day_of_week, data=new_data,
                                            FUN=mean)
    xyplot(steps ~ interval |day_of_week, data=new_mean_steps_by_interval,type="l",ylab="Number of steps",
           xlab="Time Interval",xlim=c(0,2500),ylim=c(0,250),layout=c(1,2))

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-13-1.png)
