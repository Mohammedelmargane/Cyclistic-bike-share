# Cyclistic-bike-share
Hey there! I’ve spent the past month diving into the Google Data Analytics Professional Certificate on Coursera. It’s been quite the journey — challenging and stressful at points, but incredibly rewarding in terms of learning.

In this article, I’ll be sharing my approach to a particular case study. For my toolkit, I’ll be focusing on RStudio and Excel.

ok, so why did I choose Rstudio, well first because the data set was too large for Excel to handle over 5 million rows! excel can’t handle this large dataset and I liked Rstudio when I was learnining in the Google course, Yes more than SQL so I thought i would go and do this case study in Rstudio
so let’s being

## **Scenario**

You are a junior data analyst working in the marketing analyst team at Cyclistic, a bike-share company in Chicago. The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.

About the company

In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system at anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders. Customers who purchase annual memberships are Cyclistic members.

Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers, Moreno believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, Moreno believes there is a very good chance to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.

So our goal is to convert casual riders into annual members

## ***Ask***

The future marketing program will be guided by these three questions:

1. How do annual members and casual riders use Cyclistic bikes differently?

2. Why would casual riders buy Cyclistic annual memberships?

3. How can Cyclistic use digital media to influence casual riders to become members?

Guiding questions that will help us keep the business goal clear

● What is the problem you are trying to solve?

● How can your insights drive business decisions?

## ***Prepare***

We will use Cyclistic’s historical trip data to analyze and identify trends. [Here](https://divvy-tripdata.s3.amazonaws.com/index.html) we will download the last 12 months of data From Mars 2023 till February 2024 and will extract all the CSV files into one folder and name it “Divvy_TripData”

As a disclaimer, the data has been made available by Motivate International Inc. under this license

we are not going to convert the csv files to xlsx because we can just import the csv files into RStudio using read.csv but before that, we need to install and load the required packages.

```
#installing the required packages
install.packages('tidyverse')
install.packages('janitor')
install.packages('lubridate')
install.packages('readr')
install.packages('ggplot2')
#loading the required packages
library(tidyverse)
library(janitor)
library(lubridate)
library(readr)
library(ggplot2)
```

## ***Process***

STEP 1: COLLECT DATA

I created a new Rproject and transferred all the files into that folder “instead of typing the whole file directory like this


then i used read.csv to import all the files into RStudio and also renamed all the CSV’s for better understanding

R is case sensitive so make sure you name everything with consistency don’t make my mistake with the first CSV having all small names “mar2023” and the others with a capital first letter

```
# Upload Divvy datasets (CSV files) here
 mar2023 <- read.csv("Divvy_TripData/202303.csv")
 Apr2023 <- read.csv("Divvy_TripData/202304.csv")
 May2023 <- read.csv("Divvy_TripData/202305.csv")
 Jun2023 <- read.csv("Divvy_TripData/202306.csv")
 Jul2023 <- read.csv("Divvy_TripData/202307.csv")
 Aug2023 <- read.csv("Divvy_TripData/202308.csv")
 Sep2023 <- read.csv("Divvy_TripData/202309.csv")
 Oct2023 <- read.csv("Divvy_TripData/202310.csv")
 Nov2023 <- read.csv("Divvy_TripData/202311.csv")
 Dec2023 <- read.csv("Divvy_TripData/202312.csv")
 Jan2024 <- read.csv("Divvy_TripData/202401.csv")
 Feb2024 <- read.csv("Divvy_TripData/202402.csv")
 ```
 
### Step 2 is to merge all the files into one dataset but before that, we need to check for any Data Types error and make sure that all the files have identical Data Types using ‘str()’
```
#checking data types
str(mar2023)
str(Apr2023)
str(May2023)
str(Jun2023)
str(Jul2023)
str(Aug2023)
str(Sep2023)
str(Oct2023)
str(Nov2023)
str(Dec2023)
str(Jan2024)
str(Feb2024)
```

check for any data errors and make sure that everything is the same type then move to the next step merging them into one dataset
```
##Creating new dataset name
merged_df <- bind_rows(mar2023, Apr2023, May2023, Jun2023, Jul2023, 
Aug2023, Sep2023, Oct2023, Nov2023, Dec2023, Jan2024, Feb2024)
I merge all the files into one Data Frame I called Merged_df using ‘bind_rows’
```

After we created the merged data frame, we should clean it by removing any spaces, parentheses, and removing empty columns. Using ‘clean_names’ & ‘remove_empty’
```
#Cleaning & removing any spaces
merged_df <- clean_names(merged_df)
#removing_empty
remove_empty(merged_df, which = c())
```

### STEP 3: CLEAN UP AND ADD DATA TO PREPARE FOR ANALYSIS

The data can only be aggregated at the ride level, which is too granular. We will want to add some additional columns of data — such as day, month, and year — that provide additional opportunities to aggregate the data.
```
merged_df$date <- as.Date(merged_df$started_at) #The default format is yyyy-mm-dd
merged_df$month <- format(as.Date(merged_df$date), "%m")
merged_df$day <- format(as.Date(merged_df$date), "%d")
merged_df$year <- format(as.Date(merged_df$date), "%Y")
merged_df$day_of_week <- format(as.Date(merged_df$date), "%A")
```

not let’s Add a “ride_length” calculation to merged_df (in seconds) by subtracting “ended_at” from “started_at” and naming the new columns “ride_length”

the “started_at” and “ended_at” columns were formatted as “Chr” and to do this calculation we need to convert them into “.POSIXct”

```
merged_df$started_at <- as.POSIXct(merged_df$started_at)
merged_df$ended_at <- as.POSIXct(merged_df$ended_at)
```

now let’s create a new column called “ride_length” using “difftime”
```
merged_df$ride_length <- as.numeric(difftime(merged_df$ended_at , 
merged_df$started_at , units = "secs"))
```

after looking at the new data “ride_lenght” I found that there are some rides where trip duration shows up as negative, including several hundred rides where Divvy took bikes out of circulation for Quality Control reasons. We will want to delete these rides.

to do this I will create a new version of the dataframe (v2) since data is being removed
```
merged_df_v2 <- merged_df[!(merged_df$start_station_name == 
"HQ QR" | merged_df$ride_length<0),]
```

> For clarity, we’re essentially creating a new dataframe called “merged_df_v2” from “merged_df” that excludes trip durations of 0 seconds or less.

## ***Analyze***

### STEP 4: CONDUCT DESCRIPTIVE ANALYSIS

I calculated the average ride length overall and the average ride length by casuals & members and the max and median ride length

```
#Calculating the average ride length
average_ride_length <- mean(merged_df_v2$ride_length)
```

![Average Ride Length](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/average%20ride%20length.png)


```
#Calculating the average ride length by members & casuals
avg_ride_length_by_member_casual <- aggregate(ride_length ~ member_casual 
, data = merged_df_v2, FUN = mean)
```

![Average Ride Length by members & casuals](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/average%20ride%20length%20by%20members%20%26%20casuals.png)


we can clearly see that casual members almost spend double the amount of time using the bikes as compared to annual members!

```
#Calculating the rides by day of the week
rides_by_day_of_week <- aggregate(ride_id ~ day_of_week, 
data = merged_df_v2, FUN = length)
```

![Rides by day of the week](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/rides%20by%20day%20of%20the%20week.png)


Notice that the days of the week are out of order. Let’s fix that.
```
merged_df_v2$day_of_week <- ordered(amerged_df_v2$day_of_week, 
levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
```

Finally, we created the max and median ride length
```
#Calculating the max ride length
max_ride_length <- max(merged_df_v2$ride_length)
```
![max ride length](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/the%20max%20ride%20length.png)

```
#Calculating the median ride length
median_ride_length <- median(merged_df_v2$ride_length)
```
![Median ride length](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/the%20median%20ride%20length.png)

Then I calculated which type of Rideable is most used by members and casuals
```
#Calculating the most rideable type
rideable_table <- table(merged_df_v2$rideable_type, 
merged_df_v2$member_casual)
```

![Most rideable type](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/the%20most%20rideable%20type.png)


Now let’s analyze ridership data by type and weekday
```
merged_df_v2 %>% 
  mutate(weekday = wday(started_at, label = TRUE)) %>%  
                          #creates weekday field using wday()
    group_by(member_casual, weekday) %>%  
                          #groups by usertype and weekday  
      summarise(number_of_rides = n()    
                         #calculates the number of rides and average duration 
        ,average_duration = mean(ride_length)) %>%  
                          # calculates the average duration
          arrange(member_casual, weekday) 
                            #sorts
 ```          

 ![ridership data by type and weekday](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/ridership%20data%20by%20type%20and%20weekday.png)


## ***Share***

And now to the fun part at least for me which is visualizing

Let’s Visualize the Number Of Rides by member & casual
```
merged_df_v2 %>% 
    mutate(weekday = wday(started_at, label = TRUE)) %>% 
    group_by(member_casual, weekday) %>% 
    summarise(number_of_rides = n()
              ,average_duration = mean(ride_length)) %>% 
    arrange(member_casual, weekday)  %>% 
    ggplot(aes(x = weekday, y = number_of_rides, fill = member_casual)) +
    geom_col(position = "dodge") +  
    labs(x = "Weekday" , y = " Number Of Rides") + 
    scale_fill_manual(values = c("member" = " #89ABE3" , "casual" = " #EA738D"))
 ```
I will not be explaining every line of code for the visualization but basically, we are going to use ggplot for our visualisation so I set the “x” axis to be the (weekday) and the “y” axis to be (number of rides) while using member_casual as fill I used the command “labs” to name the “x” axis and the “y” axis and the “scale_fill_manual” for colours

>The colours are
>Sky blue: #89ABE3
> Bubblegum pink: #EA738D


 ![Visualize the Number Of Rides by member & casual](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/312180f980c37d58f21ee53d770718bf73abbbdf/images/Screenshot%202024-04-12%20224703.png)

**Average Ride Duration by member & casual**
```
 merged_df_v2 %>% 
     mutate(weekday = wday(started_at, label = TRUE)) %>% 
     group_by(member_casual, weekday) %>% 
     summarise(number_of_rides = n()
               ,average_duration = mean(ride_length)) %>% 
     arrange(member_casual, weekday)  %>% 
     ggplot(aes(x = weekday, y = average_duration, fill = member_casual)) +
     geom_col(position = "dodge") +
     labs(x = "Weekday" , y = " Average Ride Duration") +
     scale_fill_manual(values = c("member" = "#89ABE3" , "casual" = "#EA738D"))
  ```

![Average Ride Duration by member & casual](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/8c707cf3f55163281cc6cc1c061f3a9fc44c0693/images/Average%20Ride%20Duration%20by%20member%20%26%20casual.png)


The overall ridership for annual members appears to remain relatively consistent throughout the week. This suggests the likelihood that annual members are using the bikes as their primary mode of transportation, such as for commuting to work

Conversely, ridership among casual members tends to be low during the weekdays but shows an increase starting from Fridays, reaching its peak on Saturdays.

We need to check for the popular stations for casuals so we can target those stations for our ads,
To find the most used stations by casuals, you can filter the dataframe by member_casual and then calculate the counts for casuals and arrange them by Desc order
```
casual_start_stations_count <- merged_df_v2 %>%
    filter(member_casual == "casual" & start_station_name != "") %>%
     group_by (start_station_name) %>%
     summarise(num_rides_started_by_casual = n()) %>%
     arrange(desc(num_rides_started_by_casual))
```
![Top 10 stations by casuals](https://github.com/Mohammedelmargane/Cyclistic-bike-share/blob/8c707cf3f55163281cc6cc1c061f3a9fc44c0693/images/Top%2010%20papular%20stations%20for%20casuals.png)


## ***Act***

Now let’s answer the questions we had

**1 How do annual members and casual riders use Cyclistic bikes differently?**
* **Ride Length**: On average, casual riders have much longer ride lengths compared to annual members. For example, on Fridays, the average ride length for casual riders is 1640.88 seconds, while for annual members it is 756.66 seconds.
* **Day of Week Usage**: Casual riders seem to use the bikes more on weekends (Saturday and Sunday), with the longest average ride lengths on those days. Annual members, on the other hand, have more consistent usage across all days of the week.

These insights suggest that casual riders tend to use the bikes for longer recreational rides, potentially on weekends, while annual members use the bikes more for shorter, utilitarian trips throughout the week.

**2 Why would casual riders buy Cyclistic annual memberships?**

There are a few potential reasons why casual riders may want to purchase Cyclistic annual memberships:

* **Cost Savings**: If casual riders use the bikes frequently, an annual membership could lead to significant cost savings compared to purchasing single-ride or full-day passes each time.
* **Convenience**: With an annual membership, riders would have the convenience of accessing the bikes anytime without having to go through the process of purchasing a pass each time.
* **Additional Benefits**: Cyclistic could potentially offer additional benefits to annual members, such as discounts, access to premium bikes, or exclusive events/experiences. These additional perks could incentivize casual riders to upgrade to an annual membership.
* **Commitment to Sustainability**: Some casual riders may be interested in making a more long-term commitment to sustainable transportation and view an annual Cyclistic membership as a way to do so.

By understanding the motivations and pain points of casual riders, Cyclistic can design its annual membership offering to be more appealing and valuable to this target group.

**3 How can Cyclistic use digital media to influence casual riders to become members?**

Cyclistic can leverage various digital media channels to reach and influence casual riders to become annual members:

* **Social Media**: Utilize platforms like Facebook, Instagram, and Twitter to showcase the benefits of the annual membership through engaging content, user-generated content, and targeted advertising. Highlight the cost savings, convenience, and exclusive perks of being a member.
* **Email Marketing**: Build an email list of casual riders and send personalized campaigns that emphasize the advantages of upgrading to an annual membership. Include special offers or promotions to incentivize the conversion.
* **Retargeting Ads**: Use retargeting ads on websites and social media to reach casual riders who have previously engaged with Cyclistic’s digital platforms. Remind them of the benefits of becoming a member and encourage them to make the switch.
* **Content Marketing**: Create valuable, informative content on Cyclistic’s website or blog that addresses the pain points and needs of casual riders. This could include guides on bike maintenance, safety tips, or city exploration ideas that showcase the value of being a member.
* **Influencer Partnerships**: Collaborate with local influencers or micro-influencers who align with Cyclistic’s brand and have an engaged audience of potential casual riders. These influencers can share their personal experiences and endorsements of the annual membership.

By leveraging a mix of digital marketing strategies, Cyclistic can effectively reach and convert casual riders into loyal annual members, ultimately driving the company’s long-term success.

> So this was my approach to this case study I hope you enjoyed it and thank you for reading till the end
