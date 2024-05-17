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

2. Broad overview about null values:
    - 74.4% of casual riders' gender have null value, and deleting null value could cause deleting values in other valueble veilds.
        ```r
        data %>% ggplot (aes(x = usertype, fill = gender)) + geom_bar()
        ```
        ![cleaning null values](./plots/cleaning-null-vals-in-2019q1-rplot1.png)

        ```r
        data %>% group_by(usertype, gender) %>% summarize(count = n(), per = n() / nrow(.) * 100)
        ```
        ```
        usertype   gender    count    per
        <chr>      <chr>     <int>  <dbl>
        1 Customer   ""        17228  4.72 
        2 Customer   "Female"   1875  0.514
        3 Customer   "Male"     4060  1.11 
        4 Subscriber ""         2483  0.680
        5 Subscriber "Female"  65043 17.8  
        6 Subscriber "Male"   274380 75.2 
        ```