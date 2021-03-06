---
title: "Untitled"
author: "Pranjal Vijay"
date: "December 12, 2017"
output: md_document
---


##Setting working directory
```{r}
setwd("C:/Users/ddddd/Reproducible peer 1")
```
##Loading the activity dataset "activity.csv" using "read.csv"
```{r}
dataset<-read.csv("activity.csv", header = T, sep = ",", stringsAsFactors = F)
```
#Get a summary of number of steps, date and interval
```{r}
dataset$date <- as.Date(dataset$date, "%Y-%m-%d")
summary(dataset)
```
#Checking for the dimensions of the activity dataset
```{r}
dim(dataset)
```

##Install the required packages


install.packages("dplyr")

install.packages("ggplot2")

##Loading the library
 library(dplyr)
 
 library(ggplot2)

##Now, start calculating the required results as gven in this assignment

##Que 1  What is the mean total number of steps taken per day?

#Part 1.Calculating the total number of steps taken per day
#Here, the missing values are not included
```{r}
totalstep <- aggregate(steps ~ date, dataset, sum)
hist(totalstep$steps, main = paste("Total Number of Steps(Per Day)"), col="pink",border="red",xlab="Total steps")
```



#part 2 Calculating the mean and median of the total number of steps taken per day

#Calculating mean

```{r}
totalstepmean <- mean(totalstep$steps)
totalstepmean
```
#Calculating median
```{r}
totalstepmedian <- median(totalstep$steps)
totalstepmedian
```

##Que 2. What is the average daily activity ?
#Part 1.Making the time series plot

```{r}
totalstepint <- aggregate(steps ~ interval, dataset, mean)
plot(totalstepint$interval,totalstepint$steps, type="l", xlab="Interval of Steps Taken", ylab="Total Steps Taken",main="Time series plot(Average of steps taken per interval)")
```


#Part 2. Determining the interval, having the maximum number of steps
```{r}
Maximumstepsint <- totalstepint[which.max(totalstepint$steps),1]
Maximumstepsint
```


##Que 3. Impute of fill the missing values(NAs)
#Part 1.Calculating the sum of total missing values(NAs)

```{r}
Sum_Missing_Values <- sum(!complete.cases(dataset))
Sum_Missing_Values
```

#Part 2. Fill in the missing values(NAs) in the dataset
```{r}
avgstepformissing <- aggregate(steps ~ interval, data = dataset, FUN = mean)
missingvaluefilling <- numeric()

```
#Now,apply looping and fill the missing values (NAs)
```{r}

for (i in 1:nrow(dataset)) {
  vari1 <- dataset[i, ]
    if (is.na(vari1$steps)) {
        steps <- subset(avgstepformissing, interval == vari1$interval)$steps
    } else {
        steps <- vari1$steps
    }

   missingvaluefilling <- c(missingvaluefilling, steps)
}
```
#Table of data after filling missing values(NAs)
```{r}
table(missingvaluefilling)
```
#First value 
```{r}
head(missingvaluefilling)
```
#Last value
```{r}
tail(missingvaluefilling)
```



#Part 3.Making a new dataset(dataset1) which also includes all the missing values(NAs) along with original data

```{r}
dataset1 <- dataset
dataset1$steps <- missingvaluefilling
```

#Part 4.Making a histogram of total steps taken per day including the missing values(NAs)
```{r}
totalstepwithNA <- aggregate(steps ~ date, data = dataset1, sum, na.rm = TRUE)
```
```{r}
#Plotting the histogram
hist(totalstepwithNA$steps, main = paste("Total Number of Steps Per Day(After inputing NAs)"), col="orange",border="green", xlab="Total steps")
```



#Part 5.calculating the mean and median total number of steps taken per day
#calculating mean
```{r}
totalstepwithNA_mean <- mean(totalstepwithNA$steps)
totalstepwithNA_mean
```
#calculating median
```{r}
totalstepwithNA_median <- median(totalstepwithNA$steps)
totalstepwithNA_median
```

#Part 6. Comparing the mean and median of both the plots above in which one doesn't have missing values while the another one includes all the missing values 

#Diffrence of mean in both plots
```{r}
Diffrence_mean <- totalstepwithNA_mean - totalstepmean
Diffrence_mean
```

#Diffrence of median in both plots
```{r}
Diffrence_median <- totalstepwithNA_median - totalstepmedian
Diffrence_median
```

##Que 4.what are the differences in activity between weekdays and weekends?

#Loading the library lattice for using the lattice graph or xyplot 

#xyplot helps to display bivariate plot and here the plot shows the #number of steps taken in weekdays and weekends together in one plot

```{r}
library(lattice)
```

#Part 1. As given on courera assignment , this step includes creating a new factor 
```{r}
Totalweekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", 
              "Friday")
dataset1$dow = as.factor(ifelse(is.element(weekdays(as.Date(dataset1$date)),Totalweekdays), "weekdays", "weekends"))
totalstepwithNA <- aggregate(steps ~ interval + dow, dataset1, mean)
```

#Part2.Ploting the result which shows the average number of steps taken per interval both in the weekends and the weekdays
```{r}

xyplot(totalstepwithNA$steps ~ totalstepwithNA$interval|totalstepwithNA$dow, main="Average number of steps taken(Per interval)",xlab="Total intervals given", ylab="Total number of steps taken",layout=c(1,2), type="l")
```




