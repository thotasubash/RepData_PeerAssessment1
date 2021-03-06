---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

activity = read.csv("C:/Users/Thota/Documents/GitHub/RepData_PeerAssessment1/activity/activity.csv")


## What is mean total number of steps taken per day?

## Calculating the total number of steps taken each day and stored in a variable
total_steps_each_day <- aggregate(steps~date, data=activity, FUN=sum, na.rm=TRUE)

## Generating the Histogram by each day
png(filename='plots/Assign1.png',width=480,height=480,units='px')
hist(total_steps_each_day$steps)
x<-dev.off()

total_steps_each_day_mean <- mean(total_steps_each_day$steps)
total_steps_each_day_median <- median(total_steps_each_day$steps)


## What is the average daily activity pattern?

## Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

five_minutes_average <- aggregate(steps~interval, data=activity, FUN=mean, na.rm=TRUE)

png(filename='plots/Assign2.png',width=480,height=480,units='px')
plot(x = five_minutes_average$interval, y = five_minutes_average$steps, type = "l") 
x<-dev.off()

## Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

max_steps <- max(five_minutes_average$steps)
for (i in 1:288) 
{
    if (five_minutes_average$steps[i] == max_steps)
        five_minute_interval_at_max_steps <- five_minutes_average$interval[i]
}
five_minute_interval_at_max_steps 


## Imputing missing values

##Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
total_na <- 0
for (i in 1:17568)
{
    if(is.na(activity$steps[i])) 
        total_na <- total_na+1 
}
total_na

##Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated.
The strategy will be to fill in the dataset the with the mean of that 5 minute interval

##Create a new dataset that is equal to the original dataset but with the missing data filled in.
activity_filled_in <- activity
for (i in 1:17568) # loop to find the na
{
    if(is.na(activity_filled_in$steps[i])) # if steps is na store the pointer 
    { 
        five_minute_pointer <- activity_filled_in$interval[i] #store the value of pointer to find the mean on five minute interval
        for (j in 1:288)  # loop to find the value of pointer on the data frame of five minute interval
        {
            if (five_minutes_average$interval[j] == five_minute_pointer) # finding the value of mean of five minute interval data frame
                activity_filled_in$steps[i] <- five_minutes_average$steps[j] # replacing the na by the mean in that fime minute interval 

        }
    }
}

##Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 
total_steps_each_day_filled_in <- aggregate(steps~date, data=activity_filled_in, FUN=sum, na.rm=TRUE)

png(filename='plots/Assign3.png',width=480,height=480,units='px')
hist(total_steps_each_day_filled_in$steps)
x<-dev.off()


## Are there differences in activity patterns between weekdays and weekends?

##Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
### creating a char vector (week_day) 
week <- wday(activity_filled_in$date)
week_day <- week
for (i in 1:17568) # loop to find the na
{
    if(week[i] == 1)
        week_day[i] <- 'weekend'
    if(week[i] == 2)
        week_day[i] <- 'weekday'
    if(week[i] == 3)
        week_day[i] <- 'weekday'
    if(week[i] == 4)
        week_day[i] <- 'weekday'
    if(week[i] == 5)
        week_day[i] <- 'weekday'
    if(week[i] == 6)
        week_day[i] <- 'weekday'
    if(week[i] == 7)
        week_day[i] <- 'weekend'
}

### Creating a new factor variable in the dataset "activity_filled_in" 
activity_filled_in$weekday <-week_day

##Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).
# finding the elements by "weekday" or "weekend"
weekday <- grep("weekday",activity_filled_in$weekday)
weekday_frame <- activity_filled_in[weekday,]
weekend_frame <- activity_filled_in[-weekday,]

## Make a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, 
##    averaged across all days (yaxis)
five_minutes_average_weekday <- aggregate(steps~interval, data=weekday_frame, FUN=mean, na.rm=TRUE)
five_minutes_average_weekend <- aggregate(steps~interval, data=weekend_frame, FUN=mean, na.rm=TRUE)

png(filename='plots/Assign4.png',width=480,height=480,units='px')
plot(x = five_minutes_average_weekday$interval, y = five_minutes_average_weekday$steps, type = "l") 
x<-dev.off()

png(filename='plots/Assign5.png',width=480,height=480,units='px')
plot(x = five_minutes_average_weekend$interval, y = five_minutes_average_weekend$steps, type = "l") 
x<-dev.off()
