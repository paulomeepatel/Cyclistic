# Cyclistic
Presented alternate options for corporate decision-making to broaden the client base. To clean, combine, and evaluate the market trend, R studio was used. Utilized ggplot2 to present data insights about trends. Recommended solution for the bike-share company's commercial issue.  

## Project Introduction

Cyclistic introduced a popular bike-share program in 2016. The initiative has expanded since then to include a fleet of 5,824 bicycles that are geo-tracked and locked into a system of 692 stations throughout Chicago.

Up to this point, Cyclistic's marketing approach focused on raising public awareness and appealing to a wide range of consumer groups. The price plans' flexibility, which included single-ride passes, full-day passes, and annual memberships, was one strategy that assisted in making these things possible. Casual riders are those who buy one-ride or all-day passes from the company. Cyclistic members are customers who purchase annual memberships.

## Purpose of Data Analysis

This report will focus on solving business problem: "What is the best approach for upgrading casual Cyclistic members into annual members?"

We would be looking deeper into the raw data to determine how casual and annual members use Cyclistic differently.

## Stakeholders and intended audience

Important stakeholders involved in overall analysis are: 
- Director of Marketing
- Cyclistic Marketing Team
- Cyclistic Executive Team
- Cyclistic Users

## Data Sources

User data from January 2021 through December 2021 has been made public. Every ride logged by Cyclistic customers is included in each data collection, which is in csv format. Motivate International Inc. and the city of Chicago have granted the public access to this data under a license, which is available online. To ensure privacy, all user personal information has been cleaned.

## Cleaning, Visualizing and Documentation of Data Analysis

## Tools for Analysis

Considering the size of the data and visualization needed to complete this analysis, R Programming is used.

Load the necessary libraries that will be utilized for the project

```
library(tidyverse)
library(lubridate)
library(janitor)
library(dplyr)
library(ggplot2)
library(readr)
```

Load all the data and combine all the data sets

```
trip21_June <- read.csv("June2021.csv")
trip21_July <- read.csv("July2021.csv")
trip21_Aug <- read.csv("Aug2021.csv")
trip21_Sept <- read.csv("Sept2021.csv")
trip21_Oct <- read.csv("Oct2021.csv")
trip21_Nov <- read.csv("Nov2021.csv")
trip21_Dec <- read.csv("Dec2021.csv")
trip22_Jan <- read.csv("Jan2022.csv")
trip22_Feb <- read.csv("Feb2022.csv")
trip22_Mar <- read.csv("Mar2022.csv")
trip22_Apr <- read.csv("April2022.csv")
trip22_May <- read.csv("May2022.csv")
```

Combine Every data set to consolidate analysis

```
trips21_22 <- rbind(trip21_June, trip21_July, trip21_Aug, trip21_Sept, trip21_Oct, trip21_Nov, trip21_Dec, trip22_Jan, trip22_Feb, trip22_Mar, trip22_Apr, trip22_May)
```

View newly created dataset
```
View(trips21_22)
```

Only keeping the important columns
```
trips21_22 <- trips21_22 %>% 
  select(c(ride_id, rideable_type, started_at, ended_at, member_casual))
```

Viewing current table

```
View(trips21_22)
```

Review of data and its parameter
```
colnames(trips21_22) #number of columns
nrow(trips21_22) #number of rows
dim(trips21_22) #dimension of the table
head(trips21_22, 6) #first 6 rows of table
str(trips21_22) #list of columns and data types
summary(trips21_22) #date and dimension 


1	99FEC93BA843FB20	electric_bike	2021-06-13 14:31:28	2021-06-13 14:34:11	
2	06048DCFC8520CAF	electric_bike	2021-06-04 11:18:02	2021-06-04 11:24:19	
3	9598066F68045DF2	electric_bike	2021-06-04 09:49:35	2021-06-04 09:55:34	
4	B03C0FE48C412214	electric_bike	2021-06-03 19:56:05	2021-06-03 20:21:55	
5	B9EEA89F8FEE73B7	electric_bike	2021-06-04 14:05:51	2021-06-04 14:09:59	
6	62B943CEAAA420BA	electric_bike	2021-06-03 19:32:01	2021-06-03 19:38:46
```

Additional columns for date and time
```
trips21_22$date <- as.Date(trips21_22$started_at)
trips21_22$month <- format(as.Date(trips21_22$date), "%m")
trips21_22$day <- format(as.Date(trips21_22$date), "%d")
trips21_22$year <- format(as.Date(trips21_22$date), "%Y")
trips21_22$day_of_week <- format(as.Date(trips21_22$date), "%A")
trips21_22$time <- format(trips21_22$started_at, format = "%H:%M")
trips21_22$time <- as.POSIXct(trips21_22$time, format = "%H:%M")
```

Calculated Field for Time of Each Ride
```
trips21_22$length_of_ride <- (as.double(difftime(trips21_22$ended_at, trips21_22$started_at)))/60
```

Checking data structure of the table columns
```
str(trips21_22)
```

Alter data type for time
```
trips21_22$length_of_ride <- as.numeric(as.character(trips21_22$length_of_ride))
```

Eliminating blank entries from the table
```
trips21_22 = subset(trips21_22, select = -c(time))
trips21_22 <- trips21_22[!(trips21_22$length_of_ride < 0),]
```

View updated table
```
View(trips21_22)
```

Observe newly created column for backup dataset
```
summary(trips21_22$length_of_ride)
 Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
 0.00     6.37    11.33    20.69    20.60 55944.15 
```

Analyze data
Calculating mean, median, max, min- figures to determine statistical speed of membership type
```
aggregate(trips21_22$length_of_ride ~ trips21_22$member_casual, FUN = mean)
aggregate(trips21_22$length_of_ride ~ trips21_22$member_casual, FUN = median)
aggregate(trips21_22$length_of_ride ~ trips21_22$member_casual, FUN = max)
aggregate(trips21_22$length_of_ride ~ trips21_22$member_casual, FUN = min)
```
Order by days of the week
```
trips21_22$day_of_week <- ordered(trips21_22$day_of_week, levels=c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"))
```

Review the table 
```
View(trips21_22)
```

Create a weekday field as well as view column specifics
```
number_of_rides = trips21_22 %>% 
mutate(day_of_week = wday(started_at, label = TRUE)) %>% 
group_by(member_casual, day_of_week) %>% 
summarize(number_of_rides = n())
```
Data Visualizations

(i) Rides per day visual

```
trips21_22$day_of_week <- format(as.Date(trips21_22$date), "%A")
trips21_22$day_of_week <- ordered(trips21_22$day_of_week, levels=c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"))
trips21_22 %>% 
group_by(member_casual, day_of_week) %>% 
summarize(number_of_rides = n()) %>% 
#order_by(day_of_week) %>% 
#arrange(day_of_week) %>% 
ggplot(aes(x=day_of_week, y = number_of_rides, fill = member_casual)) + geom_col(position = "dodge") + labs(x = "Day of the week", y = "Total number of rides", title = "Rides per day", fill = "Type of membership") + scale_y_continuous(breaks = c(250000, 400000, 550000), labels = c("250K", "400K", "550K"))
```
<img src="https://user-images.githubusercontent.com/101534066/196831361-67092975-e5ab-4282-be06-fb702cbf60d4.png" width="70%" height="70%">

The rides per day of week shows casual riders peak on Saturday and Sunday while members peak on weekdays. This implies that members majorly use Cyclistic for commutes and not leisure.

(ii) Rides per month visual
```
trips21_22 %>% 
group_by(member_casual, month) %>% 
summarize(total_rides = n(), `average_duration_(mins)` = mean(length_of_ride)) %>% 
arrange(member_casual) %>% 
ggplot(aes(x= month, y= total_rides, fill = member_casual)) + geom_col(position = "dodge") + labs(x = "Month", y = "Total number of rides", title = "Rides per month", fill = "Type of membership") + scale_y_continuous(breaks = c(100000, 200000, 300000, 400000), labels = c("100K", "200K", "300K", "400K")) + theme(axis.text.x = element_text(angle = 45))
```
<img src="https://user-images.githubusercontent.com/101534066/196831468-098fdc64-8a8f-43e2-a9a1-3e9276ee835e.png" width="70%" height="70%">

- The rides per month show that casual riders were a lot more active during the summer months than the long-term. Conversly, the winter months show very little activity on the part of the casual users. The long-term users are more active in the winter and spring months. 


(iii) Type of the bikes rented
```
trips21_22 %>% 
  ggplot(aes(x = rideable_type, fill = member_casual)) + geom_bar(position = "dodge") +
  labs(x = "Tyoe of bike", y = "Number of rentals", title = "Most rented bikes", fill = "Type of membership") + scale_y_continuous(breaks = c(500000, 1000000, 1500000), labels = c("500K", "1M", "1.5M"))
```
<img src="https://user-images.githubusercontent.com/101534066/196831530-8262839b-4f72-4cb1-962c-c509ebff4011.png" width="70%" height="70%">


- The breakdown of which type of bike is the most popular among either type of user. Showing among the two types of bikes classic and electric. both types of memberships prefer using the classic bike more so than the electric bike. The long-term members are also seen to be of the two types favors the classic bike.

(iv) Average time spend while riding bike by individuals of each membership type 
```
trips21_22 %>% 
  mutate(day_of_week = wday(started_at, label = TRUE)) %>%
  group_by(member_casual, day_of_week) %>% 
  summarize(number_of_rides = n(), average_duration = mean(length_of_ride)) %>% 
  arrange(member_casual, day_of_week) %>% 
  ggplot(aes(x = day_of_week, y = average_duration, fill = member_casual)) + geom_col(position = "dodge") + labs(x = "Days of the week", y = "Average duration- Hrs", title = "Average ride time daily", fill = "Type of membership")
```
<img src="https://user-images.githubusercontent.com/101534066/196831634-4967c4ac-2e20-4ce1-a386-3645e13167f0.png" width="70%" height="70%">


- The average ride time shows a stark difference between the casuals and members. Casuals overall spend more time using the service than their full time member counter-parts

## Key takeaways

- Casual users tended to ride more so in the warmer months of Chicago, namely June- August. Their participation exceeded that of the long term members.
- To further that the Casual demographic spent on average a lot longer time per ride than their long-term counter-parts.
- The days of the week also further shows that causal riders prefer to use the service during the weekends as their usage peaked then. The long term members conversly utilised the service more-so throughout the typical work week i.e (Monday- friday)
- Long term riders tended to stick more so to classic bikes as opposed to the docked or electric bikes.


## Recommendations

- This report recommends the following:
- Introducing plans thats may be more appealing to casuals for the summer months. This marketing should be done during the winter months in preperation.
- The casual users might be more interested in a memebrship option that allows for per-use balance card. Alternatively, the existing payment structure may be altered in order to make single-use more costly to the casual riders as well as lowering the long-term membership rate.
- Membership rates specifically for the warmer months as well as for those who only ride on the weekends would assist in targeting the casual riders more specifically. 
