```r
library(tidyverse)
data1 <- read.csv("./cleaned/divvy_trips_2019_Q1.csv")
data2 <- read.csv("./cleaned/divvy_trips_2019_Q2.csv")
data3 <- read.csv("./cleaned/divvy_trips_2019_Q3.csv")
data4 <- read.csv("./cleaned/divvy_trips_2019_Q4.csv")
data <- bind_rows(data1, data2, data3, data4)
write.csv(data, "./cleaned/all_trips.csv", row.names = FALSE)
rm(data, data1, data2, data3, data4)

data <- read.csv("./cleaned/all_trips.csv")

#take the week and month of the day
library(lubridate)
data <- data %>% mutate(week = wday(as.Date(start_time), week_start = 1))
data <- data %>% mutate(month = month(as.Date(start_time)))
data <- data %>% mutate(hour = hour(start_time))
data <- mutate(hour = as.numeric(substr(tripduration, 12, 13)))
data <- data %>% filter(!(end_time <= start_time))
data <- data %>% mutate(tripdu_hr = as.numeric(substr(tripduration, 1, 2)))
data <- data %>% mutate(tripdu_in_sec = as.numeric(difftime(as.POSIXct(end_time, format = "%Y-%m-%d %H:%M:%S"), as.POSIXct(start_time, format = "%Y-%m-%d %H:%M:%S"), units = "secs")))

#identified invalied date time values in start_time and end_time and deleted them (18 rows deleted)
data <- data %>% filter(!(is.na(tripdu_in_sec))) %>% summarize(n())
write.csv(data, "./analytics/all_trips.csv", row.names = FALSE)

#analyze with weekly data
temp1 <- data %>% group_by(week) %>% summarize(total = sum(tripdu_in_sec), mean = mean(tripdu_in_sec))
write.csv(temp1, "./analytics/weekly_ride_time.csv", row.names = FALSE)

temp1 <- data %>% group_by(week, usertype) %>% summarize(avg = mean(tripdu_in_sec)) %>% pivot_wider(names_from = usertype, values_from = avg)
write.csv(temp1, "./analytics/weekly_ride_time_usertype.csv", row.names = FALSE)

#analyze weekly data where tripduration < "12:00:00"
temp1 <- data %>% filter(tripduration < "24:00:00") %>%  group_by(week) %>% summarize(total = sum(tripdu_in_sec), mean = mean(tripdu_in_sec))
write.csv(temp1, "./analytics/weekly_ride_time_filtered.csv", row.names = FALSE)

temp1 <- data %>% filter(tripduration < "12:00:00") %>%  group_by(week, usertype) %>% summarize(avg = mean(tripdu_in_sec)) %>% pivot_wider(names_from = usertype, values_from = avg)
write.csv(temp1, "./analytics/weekly_ride_time_usertype_filtered.csv", row.names = FALSE)

#analyze count of trips taken each week
temp1 <- data %>% group_by(week) %>% summarize(count = n())
write.csv(temp1, "./analytics/weekly_ride_count.csv", row.names = FALSE)

temp1 <- data %>% group_by(week, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = usertype, values_from = count)
write.csv(temp1, "./analytics/weekly_ride_count_by_usertype.csv", row.names = FALSE)

#monthly and weekly distribution of ride count:
temp1 <- data %>% group_by(month, week) %>% summarize(n())
write.csv(temp1, "./analytics/week_monthly_ride_count.csv", row.names = FALSE)

temp1 <- data %>% group_by(month, week, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = usertype, values_from = count)
write.csv(temp1, "./analytics/week_monthly_ride_count_by_usertype.csv", row.names = FALSE)

#montly distribution of rides
temp1 <- data %>% group_by(month, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = usertype, values_from = count)
write.csv(temp1, "./analytics/monthly_ride_count_by_usertype.csv", row.names = FALSE)

#analyze most active hour
temp1 <- data %>% group_by(hour,usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = usertype, values_from = count)
write.csv(temp1, "./analytics/active_hour_by_usertype.csv", row.names = FALSE)
temp1 <- data %>% filter(week <= 5) %>% group_by(week, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = usertype, values_from = count)
write.csv(temp1, "./analytics/active_hour_by_weekdays.csv", row.names = FALSE)

temp1 <- data %>% filter(week <= 5, usertype == "Customer") %>% group_by(week, hour, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = hour, values_from = count)
write.csv(temp1, "./analytics/active_hour_by_weekdays_cus.csv", row.names = FALSE)

temp1 <- data %>% filter(week > 5, usertype == "Customer") %>% group_by(week, hour, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = hour, values_from = count)
write.csv(temp1, "./analytics/active_hour_by_weekend_cus.csv", row.names = FALSE)

temp1 <- data %>% filter(week > 5, usertype == "Subscriber") %>% group_by(week, hour, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = hour, values_from = count)
write.csv(temp1, "./analytics/active_hour_by_weekend_sub.csv", row.names = FALSE)

temp1 <- data %>% filter(week <= 5, usertype == "Subscriber") %>% group_by(week, hour, usertype) %>% summarize(count = n()) %>% pivot_wider(names_from = hour, values_from = count)


#analyze trip duration data

temp2 <- data %>%
  group_by(tripdu_hr, usertype) %>%
  summarize(count = n(), .groups = 'drop') %>%
  pivot_wider(names_from = usertype, values_from = count, values_fill = list(count = 0))
write.csv(temp2, "./analytics/trip_hr_count.csv", row.names = FALSE)

#analyze with the station info
data_from_station <- data %>% group_by(from_station_id, from_station_name) %>% summarize(count = n())
write.csv(data_from_station, "./analysis/from_station_count.csv", row.names = FALSE)

data_from_station_by_usertype <- data %>% group_by(from_station_id, from_station_name, usertype) %>% summarize(count = n())
write.csv(data_from_station_by_usertype, "./analysis/from_station_count_by_usertype.csv", row.names = FALSE)

temp1 <- data %>% select(tripduration) %>% arrange(desc(tripduration))
```