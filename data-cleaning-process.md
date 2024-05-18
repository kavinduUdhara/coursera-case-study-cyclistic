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
        ```perl
         gender         birthyear 
         19711             18023
        ```
    - Identified null values in the "gender" and "birthyear" feilds (there are 365069 total rows):

        |feild | null_val | % |
        |------|----------|---|
        |gender|19711     |5.4%|
        |birthyear|18023  |4.9%|

2. Visualization of null values in `gender` feild:
    - summarized gender distribution by usertype:
        ```r
        data %>% ggplot (aes(x = usertype, fill = gender)) + geom_bar()
        ```
        ![cleaning null values](./plots/cleaning-null-vals-in-2019q1-rplot1.png)

        ```r
        data %>% group_by(usertype, gender) %>% summarize(count = n(), per = n() / nrow(.) * 100)
        ```
        ```perl
          usertype   gender    count    per
          <chr>      <chr>     <int>  <dbl>
        1 Customer   ""        17228  4.72 
        2 Customer   "Female"   1875  0.514
        3 Customer   "Male"     4060  1.11 
        4 Subscriber ""         2483  0.680
        5 Subscriber "Female"  65043 17.8  
        6 Subscriber "Male"   274380 75.2 
        ```
        Zoom in "Customer" user type (casual riders):
        ```r
        data %>% group_by(usertype, gender) %>% filter(usertype == "Customer") %>% summarize(count = n(), per = n() / nrow(.) * 100)
        ```
        ```perl
          usertype gender   count   per
          <chr>    <chr>    <int> <dbl>
        1 Customer ""       17228 74.4 
        2 Customer "Female"  1875  8.09
        3 Customer "Male"    4060 17.5 
        ```

3. Handeling null values in `gender` feild.
    - Due to the high percentage of null values for casual riders, deleting those rows could lead to a loss of valuable data.
    - Insted considered: filling missing values with a placeholder value. ("Unknown")
      ```r
      cleaned_data <- data %>% mutate(gender = ifelse(gender == "", "unknown", gender))
      ```

4. Visualizing values in `birthyear` feild.
    - Identified out of range value on birthyear feild.
      ```r
      summary_birthyr <- data %>% group_by(birthyear) %>% summarize(count = n())

      summary_birthyr %>% ggplot(aes(x = birthyear, y = count)) + geom_col() + geom_smooth(mehtod = "loess", se = FALSE)
      ```
      **Birth year disbribution of each ride**:
      ![cleaning-null-vals-in-2019q1-rplot2-withannotation](./plots/cleaning-null-vals-in-2019q1-rplot2-withannotation.png)
    - Analyzed further with converting birthyer to age, for broad understanding.
      ```r
      data <- data %>% mutate(age = 2019 - as.numeric(birthyear))
      ggplot(data, aes(x = age, fill = usertype)) + geom_histogram(binwidth = 5, position = "dodge") +  labs(title = "Age Distribution by User Type", x = "Age", y = "Count")

      summary(data$age)
      ```
      ```
       Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
      16.00   29.00   34.00   37.33   44.00  119.00   18023 
      ```

5. Reassign unreasonable ages to NA.
    - To maintain data intergrity of the dataset unreliable age data converted into null values.
      ```r
      cleaned_data <- data %>% mutate(birthyear = ifelse(age > 100 | age < 10, NA, birthyear), age = ifelse(age > 100 | age < 10, NA, age))
      ```

6. Handeling null values in `birthyear` feild.
