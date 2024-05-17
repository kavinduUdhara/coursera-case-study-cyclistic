# Dataset - Divvy_Trips_2019_Q1
This dataset contains trip data about first quarter of 2019.sa

1. Identified null values
        
    - Used R (Programming lang.) to load and inspect the dataset.
        ```r
        library(tidyverse)
        data <- read.csv("./csv/divvy_trips_2019_Q1.csv")
        ```
    - Checked for missing values with following code
        ```r
        null_values <- sapply(data, function(x) sum(is.na(x) | x == ""))
        null_values
        ```
    - Identified null values in the "gender" and "birthyear" feilds (there are 365069 total rows):

        |feild | null_val | % |
        |------|----------|---|
        |gender|19711     |5.4%|
        |birthyear|18023  |4.9%|
