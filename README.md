# Summary of Analysis for Cyclistic Bike Share

**Frequency of Usage:** Members use the bike-sharing service more frequently than casual users throughout the year. This indicates that members utilize the service regularly, likely for commuting or other routine activities, while casual users ride less consistently, possibly for leisure or occasional needs.
 
**Preference for Bike Types:** Members predominantly use classic bikes and electric bikes, with minimal usage of docked bikes. This suggests that members prefer these bike types for their regular rides. Conversely, casual users show a higher preference for electric bikes, indicating a preference for convenience or novelty.

**Weekend vs. Weekday Riding:** Casual users tend to take rides more frequently on weekends, indicating a preference for weekend activities or leisurely rides. In contrast, members maintain more consistent riding patterns throughout the week, indicating adherence to weekday routines.

**Ride Duration:** Interestingly, casual users tend to ride for longer durations on average compared to members. This could imply that casual users are more inclined towards exploration or leisurely rides, while members may prioritize efficiency or shorter commutes.

**Seasonal Variation:** Ridership peaks in June and July, likely due to favorable weather conditions, while it declines in December, possibly due to colder temperatures. This seasonal trend aligns with common expectations regarding outdoor activities and weather preferences.

**Usage Patterns by Day:** Tuesday to Thursday emerges as the preferred riding days for members, indicating a strong correlation with weekday commuting patterns. This suggests that members primarily use the bikes for work-related commutes during weekdays.

**Preference for Classic Bikes:** Both members and casual riders show a preference for classic bikes as their top choice for rentals. This could be due to the familiarity and versatility of classic bikes, making them suitable for various riding needs and preferences

* Overall, the analysis highlights distinct usage patterns and preferences between members and casual users, as well as variations across different days and seasons. Understanding these patterns can inform targeted marketing strategies, service improvements, and resource allocation to better cater to the needs and preferences of different user segments.


# My Recommendations Based On My Analysis

**Membership Promotion:**
Encourage casual users to become members by highlighting the benefits of membership, such as more frequent access to bikes and potentially lower costs per ride. Offer incentives or discounts for signing up for memberships to attract casual users.

**Bike Type Availability:**
Ensure an adequate supply of classic and electric bikes, as these are preferred by both members and casual users. Monitor bike availability and redistribute bikes as needed to high-demand areas, especially during peak riding times

**Weekend Engagement:**
Organize special events or promotions targeted towards casual users during weekends to capitalize on their higher weekend riding frequency. Offer themed rides, discounts, or partner with local events to attract more riders during weekends.
Target Marketing: Concentrate marketing efforts on areas with a high concentration of casual users, highlighting the perks of membership and offering exclusive promotions. This strategy maximizes marketing impact while minimizing costs.


 # Data Wrangling Change Log
* Data Range: 2023-04-01 to 2024-04-3
 * Total rows : 6165202
* I raised the following concern (about the data) with Lily Moreno(The director of marketing): **1652 rows with missing Starting Station names and ID**
* I was advised by Lily Moreno that we could ignore rows with trip duration <=0. We could also ignore rows with missing start_station_name, end_station_name and also ignore start_stations_id, end_start_id.
* Cleaning reduced rows to 6163550



# R Studio
* I downloaded RStudio for the desktop version.
* Install RStudio on my desktop.

**Make sure you create a directory for your project**
* The far top-right has a tab just below the RStudio "Close tab." Click it > New Project > New Directory > New Project > Name your directory and its location > Create Project.
  
**Installing Packages:**
* install.package(“tideverse”)
* install.packages("readxl") * readxl is for reading Excel file
* install.packages("sqldf") * installing SQL package
* install.packages("dplyr") * For data manipulation
* install.packages("hms") *calculate the time difference between two datetimes
  
**Load packages:**
* library(tidyverse)
* library(readxl)
* library(sqldf)
* library(dplyr)
* library(hms)
  
**Uploading:**
* Note: The point of this set of instructions is to answer "In what ways do members and casual riders use Divvy bikes differently?". We can use R to answer other questions as well.
  
# Import previous 13 months data:
# import data from external files into R for analysis
* Divvy202401 <- read.csv("...///202401-divvy-tripdata.csv")
* Divvy202402 <- read.csv("...///202402-divvy-tripdata.csv")
* Divvy202403 <-read.csv("...///202403-divvy-tripdata.csv")
* Divvy202404 <-read.csv("...///202404-divvy-tripdata.csv")
* Divvy202304 <-read.csv("...///202304-divvy-tripdata.csv")
* Divvy202305 <-read.csv("...///202305-divvy-tripdata.csv")
* Divvy202306 <-read.csv("...///202306-divvy-tripdata.csv")
* Divvy202307 <-read.csv("...///202307-divvy-tripdata.csv")
* Divvy202308 <-read.csv("...///202308-divvy-tripdata.csv")
* Divvy202309 <-read.csv("...///202309-divvy-tripdata.csv")
* Divvy202310 <-read.csv("...///202310-divvy-tripdata.csv")
* Divvy202311 <-read.csv("...///202311-divvy-tripdata.csv")
* Divvy202312 <-read.csv("...///202312-divvy-tripdata.csv")

# Merging all 13 Data into One Data Frame:
**Define the directory containing your data files(making vector)**

*It helps in organizing and locating the files you need to work with*
* data_dir <- "...//Case study/Bike sharing/divvy-tripdata/Case_study_data"

 **List all CSV files in the directory:** This step lists all the CSV files in the specified directory. Using pattern = "*.csv" ensures that only CSV files are listed, and full.names = TRUE gives the full file path for each file.
* file_list <- list.files(data_dir, pattern = "*.csv", full.names = TRUE)

**Read the data files into a list of data frames:** *This step reads each CSV file into a separate data frame and stores them in a list. Using lapply allows you to apply the read.csv function to each file in the list.*
* data_list <- lapply(file_list, read.csv)

 **Initialize a list to store column names**: *This step initializes an empty list that will later be used to store the column names of each data frame. This can help in identifying and managing any differences in column names across the files*
* column_names_list <- list()

**Loop through each file and read the column names** *This loop iterates over each file in file_list, reads the file into a data frame, extracts the column names, and stores them in the column_names_list*
* for (file in file_list) {data <- read.csv(file)column_names <- colnames(data) column_names_list[[file]] <- column_names}
  
**Create a data frame to compare column names:** *This creates a data frame column_names_df that lists each file and its corresponding columns. This makes it easier to compare and visualize which columns are present in each file.*
* column_names_df <- data.frame( file =rep(names(column_names_list), sapply(column_names_list, length)),column = unlist(column_names_list))

**Check if all files have the same columns:** *common_columns will store the names of columns that are common across all files.*
* common_columns <- Reduce(intersect, column_names_list)

**checks if all files have the same columns by comparing their column names with the common columns identified earlier:** *This code snippet is a part of the data validation process, ensuring that all files have consistent column names before merging them into a single data frame* 
* if (all(sapply(column_names_list, function(x) identical(common_columns, x)))) {print("All files have the same columns.")} else { print("There are differences in the column names across files.") print(column_names_df)}

# Combining data and making new Data Frame

**Making one large data frame:** *Combine all data frames into one*
* combined_data <- bind_rows(data_list)

# Convert Data/Time stamp to Date/Time
**Convert started_at  and ended_at to date-time format**
* combined_data$started_at1 <- ymd_hms(combined_data$started_at)
* combined_data$ended_at1 <- ymd_hms(combined_data$ended_at)
  
**Extract year and month and day_of_week from the started_at column**
**Extract year**
* combined_data$year <- year(combined_data$started_at1)
  
 **Extract month name**
* combined_data$month_name <- month(combined_data$started_at1, label = TRUE, abbr = FALSE)
  
 **Extract day of the week**
* combined_data$day_of_week <- weekdays(combined_data$started_at1)
  
**Calculate time difference**
* combined_data$Ride_time  <- as_hms(difftime(combined_data$ended_at1, combined_data$started_at1))

# Data Cleaning 
**checking negative and zero ride time by using the where function in Sqldf**
* sqldf("SELECT 'checking negative and zero ride time ' ,count (*)
      from  (select started_at1 , ended_at1 FROM combined_data
                where Ride_time  <=0)")

 **Cleaning data based on ride time by using condition function in R**
* clean_data_1 <- combined_data[combined_data$Ride_time >0,]

# Analysis and Visualization 
**Using Count function to count number of rows:**
*Use sqldf to count the total number of rows as 'total rows' title.*
* sqldf("SELECT 'total_rows', COUNT(*) AS total_rows FROM clean_data_1")

**Using SELECT DISTINCT to distinct value in ride_id** *Use sqldf to find distinct values in the ride_id column with 'distinct_ids' title*
* sqldf("SELECT DISTINCT 'distinct_ids',COUNT(ride_id) FROM clean_data_1")
  
**Time for descriptive analysis on ride_time (all figures in hms)**
 **Find maximum value in Ride_time  column**
* max_value <- as_hms (max(clean_data_1$Ride_time ))
  
**Find minimum value in Ride_time  column**
* min_value <- as_hms (min(clean_data_1$Ride_time ))
  
**Find mean value  in Ride_time  column**
* mean_value <-as_hms ( mean(clean_data_1$Ride_time ))

# Data aggregation and Pivoting for pie chart
 **Creating a pivot table with average, max, min and count**
* pivot_table_all <- clean_data_1 %>% group_by(member_casual, day_of_week) %>% summarise(count = n(),average = mean(as.numeric(Ride_time ), na.rm = TRUE), max_value=max(as.numeric(Ride_time ), na.rm = TRUE),min_value=min(as.numeric(Ride_time ), na.rm = TRUE) ) %>%
  pivot_wider(names_from = member_casual, values_from = c(count, average,max_value,min_value), values_fill = list(count = 0, average = 0,max_value=0, min_value=0))
  
**Convert average back to hms from above pivot table.**
* pivot_table_all <- pivot_table_all %>% mutate(across(starts_with("average_"), ~ as.hms(.))) %>% mutate(across(starts_with ("max_value_"), ~ as.hms(. ))) %>%   mutate(across(starts_with ("min_value_"), ~ as.hms(.)))
# Pie chart: 
**Counting different types of members and bikes, then creating  a pie chart visualizing the relationship between rider types and bike types**

**Count the number of each type of member i.e casual and member**
* count_C_M <- table(clean_data_1$member_casual)
* print(count_bike_cm)
  
**Count the number of each type of bike**
* count_bike <- table(clean_data_1$rideable_type)
* print(count_bike)
  
**Count which type of bike is used by member and casual rider**
* count_bike_cm <- table(clean_data_1$rideable_type , clean_data_1$member_casual)
* print(count_bike_cm)
  
**Convert the table into a vector for plotting**
* counts <- as.vector(count_bike_cm)
  
**Calculate percentages for labeling the pie chart**
* total <- sum(counts)
* percentages <- round(counts / total * 100)
  
**Get the combinations of column and row names**
* labels <- outer(rownames(count_bike_cm), colnames(count_bike_cm), paste, sep = "-")
  
**Format labels with percentages**
* labels_with_percentage <- paste(labels, "\n", percentages, "%", sep = "")
  
**Create a pie chart with custom labels**
* pie(counts, labels = labels_with_percentage, main = "Pie Chart with Rider Types and Bike Types ")

# Creating a pivot table to see how different bikes are used by day of week(count) and on  average,max and min ride time.
* pivot_table_r_t <- clean_data_1 %>%
  group_by(rideable_type, day_of_week) %>%
  summarise(  count = n(), average = mean(as.numeric(Ride_time ), na.rm = TRUE),  max_value=max(as.numeric(Ride_time ), na.rm = TRUE),   min_value=min(as.numeric(Ride_time ), na.rm = TRUE) ) %>%  pivot_wider(names_from = rideable_type, values_from = c(count, average,max_value,min_value), values_fill = list(count = 0, average = 0,max_value=0, min_value=0))
 Convert average back to hms 
pivot_table_r_t <- pivot_table_r_t %>%
 mutate(across(starts_with("average_"), ~ as.hms(.))) %>%  mutate(across(starts_with ("max_value_"), ~ as.hms(. ))) %>%    mutate(across(starts_with ("min_value_"), ~ as.hms(.)))


# Filtering, Summarizing, and Pivoting Data for Different Bike Types
**Combining the two columns into one category:** *Combining member_casual and    rideable_type into a single column named category*
* clean_data_1$category <- paste(clean_data_1$member_casual, clean_data_1$rideable_type, sep = "-")

 # Filtering the Data by Bike Type and creating Pivot table:*Creating Separate Data Frames for Each Bike Type and  Creating a pivot table by rider_type to compare between between casual and member by  DAY OF WEEK*
 
**Creating data frame for electric type bike**
* clean_data_electric <- clean_data_1 %>%
  filter(rideable_type =="electric_bike")
  
 **Creating a pivot table for electric rider_type to compare between casual and member BY DAY OF WEEK.**
* pivot_table_electric<-clean_data_electric%>%
  group_by(member_casual, day_of_week) %>%
  summarise(count=n(),average=mean(as.numeric(Ride_time),na.rm=TRUE),max_value=max(as.numeric(Ride_time), na.rm=TRUE),min_value=min(as.numeric(Ride_time),na.rm= TRUE))%>%
  pivot_wider(names_from=member_casual, values_from=c(count,average,max_value,min_value),values_fill= list(count = 0, average = 0,max_value=0, min_value=0))
   
 **Convert average back to hms**
* pivot_table_electric<-pivot_table_electric%>%
  mutate(across(starts_with("average_"),~as.hms(.)))%>% mutate(across(starts_with("max_value_"),~as.hms(.)))%>%mutate(across(starts_with("min_value_"), as.hms(.)))
  
**Print the pivot table**
* print( pivot_table_electric)

**Creating data frame for docked type bike**
*clean_data_docked <- clean_data_1 %>%
 filter(rideable_type =="docked_bike")
 
**Creating a pivot table for docked rider_type to compare between between casual and member  DAY OF WEEK**
* pivot_table_docked<-clean_data_docked%>%
   group_by(member_casual,day_of_week)%>%
   summarise(count=n(),average=mean(as.numeric(Ride_time),na.rm=TRUE),max_value=max(as.numeric(Ride_time),na.m=TRUE),min_value=min(as.numeric(Ride_time),na.rm=TRUE)%>%  pivot_wider(names_from=member_casual,values_from= 
    c(count,average,max_value,min_value),values_fill=list(count=0,average= 0,max_value=0, min_value=0))
 
 
**Convert average back to hms**
* pivot_table_docked <-  pivot_table_docked %>% mutate(across(starts_with("average_"), ~ as.hms(.))) %>% mutate(across(starts_with ("max_value_"), ~ as.hms(. ))) %>%    mutate(across(starts_with ("min_value_"), ~ as.hms(.)))
 
 
**Print the pivot table**
 * print( pivot_table_docked)

**creating data frame for classic type bike:**
* clean_data_classic <- clean_data_1 %>%
  filter(rideable_type =="classic_bike")

 **Creating a pivot table for docked rider_type to compare between between casual and member  DAY OF WEEK**
* pivot_table_classic <- clean_data_classic %>% group_by(member_casual, day_of_week) %>%
   summarise(count = n(),
   average = mean(as.numeric(Ride_time ), na.rm = TRUE),  max_value=max(as.numeric(Ride_time ), na.rm = TRUE),min_value=min(as.numeric(Ride_time ), na.rm = TRUE)  ) %>%
 pivot_wider(names_from = member_casual, values_from = c(count, average,max_value,min_value), values_fill = list(count = 0, average = 0,max_value=0, min_value=0))
 
 **Convert average back to hms**
 * pivot_table_classic <-  pivot_table_classic %>% mutate(across(starts_with("average_"), ~ as.hms(.))) %>%  mutate(across(starts_with ("max_value_"), ~ as.hms(. ))) %>%    mutate(across(starts_with ("min_value_"), ~ as.hms(.)))
 
**Print the pivot table**
* print( pivot_table_classic)
  
#  Pivoting table and Creating Bar graph  for Count_of_ride  by Month between casual and member.

**Creating a pivot table for electric rider_type to compare between between casual and member BY MONTH.**
 * pivot_table_electric_month <- clean_data_electric %>%
   group_by(member_casual, month_name) %>%
   summarise(
     count = n(),
     average = mean(as.numeric(Ride_time ), na.rm = TRUE), 
     max_value=max(as.numeric(Ride_time ), na.rm = TRUE),
     min_value=min(as.numeric(Ride_time ), na.rm = TRUE)
   ) %>%
   pivot_wider(names_from = member_casual, values_from = c(count, average,max_value,min_value), values_fill = list(count = 0, average = 0,max_value=0, min_value=0))
 
 
 **Convert average back to hms if needed# Convert back to hms**
 
 pivot_table_electric_month <-   pivot_table_electric_month %>%
   mutate(across(starts_with("average_"), ~ as.hms(.))) %>% 
   mutate(across(starts_with ("max_value_"), ~ as.hms(. ))) %>%   
   mutate(across(starts_with ("min_value_"), ~ as.hms(.)))
 
 ** Print the pivot table**
 print( pivot_table_electric_month)

# For creating Bar charts:
   # Creating bar chart for Electric ride type by month 
      (**Melt the data for easier plotting with ggplot2**)
* library(reshape2)
 * pivot_table_electric_month_melted <-melt(pivot_table_electric_month, id.vars = "month_name")
 
**Filter for counts to create a bar chart**
 * counts_data <- pivot_table_electric_month_melted %>%
   filter(grepl("count_", variable))
 
  **Create the bar chart**
 * ggplot(data = counts_data, aes(x = month_name, y = value, fill = variable)) + geom_bar(stat = "identity", position = "dodge") +
   labs(title = "Comparison of Electric Bike Usage by Month and Rider Type",
        x = "Month",
        y = "Count of Rides",
        fill = "Rider Type") + scale_y_continuous(labels = scales::comma) + # Format y-axis labels
   theme_minimal()

 # Creating bar chart for docked ride type by month 
 **Melt the data for easier plotting with ggplot2**
 * library(reshape2)
  * pivot_table_docked_month_melted <- melt(pivot_table_docked_month, id.vars = "month_name")
 
 **Filter for counts to create a bar chart**
 * counts_data <- pivot_table_docked_month_melted %>%
   filter(grepl("count_", variable))
 
 **Create the bar chart**
 * ggplot(data = counts_data, aes(x = month_name, y = value, fill = variable)) +
   geom_bar(stat = "identity", position = "dodge") +
   labs(title = "Comparison of docked Bike Usage by Month and Rider Type",
        x = "Month",
        y = "Count of Rides",
        fill = "Rider Type") + scale_y_continuous(labels = scales::comma) + # Format y-axis labels
   theme_minimal()
 
  # Creating bar chart for docked ride type by month 
 **Melt the data for easier plotting with ggplot2**
 * library(reshape2)
 * pivot_table_classic_month_melted <- melt(pivot_table_classic_month, id.vars = "month_name")
 
 **Filter for counts to create a bar chart**
 * counts_data <- pivot_table_classic_month_melted %>%
   filter(grepl("count_", variable))
 
 **Create the bar chart**
 * ggplot(data = counts_data, aes(x = month_name, y = value, fill = variable)) +
   geom_bar(stat = "identity", position = "dodge") +
   labs(title = "Comparison of classic Bike Usage by Month and Rider Type",
        x = "Month",
        y = "Count of Rides",
        fill = "Rider Type") + scale_y_continuous(labels = scales::comma) + # Format y-axis labels
   theme_minimal()
