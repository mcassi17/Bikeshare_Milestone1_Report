# Capital Bikeshare Ride Time Prediction
## Problem and Question
Within the last few months, there has been an influx of bikesharing companies that have come into the Washington D.C. area. The most popular, Capital Bikeshare, provides all of their data online for free. With all of this available data, there are no tools to predict how long a ride might take while traveling between two Capital Bikeshare docking stations. The project will look to answer the question of how long will it take to ride between two stations. The prediction is for riders that want to know how long a ride might take based on time of day, day of week, weather, and ride distance.

## Data
Capital Bikeshare provides all of their rider data on their [website](https://www.capitalbikeshare.com/system-data), which includes the start time, start station, end time, end station, and member type (casual and registered). There are 4 datasets for each quarter of the year (Q2 from 2011, Q3 from 2011, Q4 from 2011, and Q1 from 2012). In addition to the bikeshare data, the [University of California Irvine](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset) has a dataset that has weather data for 2011 and 2012 for bikeshare rides. This dataset contains information about the weather like temperature and humidity as well as information regarding holidays and workdays. The last dataset used in this initial report is the latitude and longitude data for each of the bikeshare stations from [Open Data DC](http://opendata.dc.gov/datasets/capital-bike-share-locations). These datasets can be found in the [data folder](https://github.com/mcassi17/Bikeshare_Milestone1_Report/tree/master/Data) in this repository.
<br>
<br>
Traffic data could be another dataset that could provide additional insights as to what increases or decreases the ride times of Capital Bikeshare bikes. Building a dataset using more recent bikeshare and weather could be useful in seeing if the analysis presented in this document holds true in later years.

## Cleaning Data
The first step in this project was to load the data and clean it using Python. The following section describes the process of cleaning the datasets and joining the relevant columns together.
<br>
<br>
The quarterly data and UCI weather dataset contain date columns that needed to be loaded in as datetime objects to make it easier for filtering and merging of the datasets. To handle the date columns, the partse_dates attributes were used on in the read_csv functions. This created all date columns as datetime objects in Python. There were not dates included in the latitude and longitude dataset.
<br>
<br>
When inspecting the first few rows of each dataframe, each datasets have columns that are not necessary for the analysis and prediction aspects of the project. The UCI weather dataset contains extra columns like month, year, and instant (an index column). The bikeshare dataset contains additional columns like duration (text field) and bike serial number. The latitude and longitude dataset contained extra columns about the different bikeshare docking stations outside of the latitudes and longitudes. All of these columns were removed as they would not impact the analysis to be performed. In addition to removing columns, some columns had to be renamed so all columns match, which is needed for the the joining of the datasets. 
<br>
<br>
Each dataset did not have many missing values. The bikeshare data contained 11 rows that did not have a final end point. These rows were removed from the bikeshare dataset. The UCI and lat/lon data did not have any null or missing values. 
<br>
<br>
Prior to joining the datasets together, the date columns from all datasets needed to be in the same format. The UCI weather dataset date format is YYYY-MM-DD, while the Bikeshare datasets used a YYYY-MM-DD HH:MM:SS format. Two additional columns were created in the Bikeshare datasets with the YYYY-MM-DD format. The original columns with the hours, minutes, and seconds were kept. In addition to creating those two columns, another columns was created for the time difference, in minutes, based on parsing the duration column (string like '0h 12min. 33sec.'). After calculating the ride time, there were values of 0, which accounted for 42 rows. These rows were removed.
<br>
<br>
The notebooks used to clean up the quarterly data are named [Bikeshare_cleaning_2012_Q1.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Bikeshare_cleaning_2012_Q1.ipynb), [Bikeshare_cleaning_Q2.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Bikeshare_cleaning_Q2.ipynb), [Bikeshare_cleaning_Q3.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Bikeshare_cleaning_Q3.ipynb), and [Bikeshare_cleaning_Q4.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Bikeshare_cleaning_Q4.ipynb). The cleaning of the latitude and longitude data is found in the file named [Add_Lat_Lon.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Add_Lat_Lon.ipynb).

### Joining Datasets
Each quarterly dataset was joined with the UCI data using the start date in the bikeshare datasets and the date column from the UCI data. Once this was done, the 5 datasets were concatenated to create the full dataset of rides and UCI weather data. 
<br>
<br>
The next two joins were used to combine the latitudes and longitudes of all of the start and end stations for each ride. The first join was to combine the start station latitudes and longitudes with the full dataset. Once this was completed, the end station latitudes and longitudes were combined with the dataset. The joining of the data can be found in the files named [Combine_Quarters.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Combine_Quarters.ipynb), [Data_Wrangling_Report.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Data_Wrangling_Report.ipynb), and [Add_Lat_Lon.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Add_Lat_Lon.ipynb).

### Additional Data Cleaning
Once the datasets were all combined, there were some rows that did not have latitudes and longitudes, meaning these stations existed in 2011 and 2012 but no longer exist in 2017. These rows were removed from the dataset (144k rows).
<br>
<br>
An additional column was created in the dataset, miles between the start and end station, using the latitudes and longitudes of the stations. The formula used to calculate this was the Vincenty distance from the geopy package in Python. This additional column creation and cleaning can be found in the file named Add_Lat_Lon.ipynb. The full report about the data wrangling effort can be found in the file named [Data_Wrangling_Report.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Data_Wrangling_Report.ipynb).

## Exploratory Analysis and Statistical Inference
After cleaning, merging, and updating the data sets, the next step was to perform graphical exploratory data analysis to determine what variables may impact the ride time. Statistical inference was then done to prove or reject the evidence from the exploratory data analysis.

### Initial Findings
Through the graphical analysis, there were differences in ride times based on seasons (spring, summer, fall, and winter), workdays (not a workday and workday), holidays (not holidays and holidays), and weather categories (sunny, cloudy, and rainy). The differences were smaller than the difference between casual and registered riders.  In addition to these categorical variables, humidity, temperature, and wind speed were also looked at. The graphical analysis showed that there might be a correlation between all three variables and the ride times. 
<br>
<br>
The member type (casual and registered riders) provided the largest difference in ride times. When the graphical analysis with the season, workday, weather category, and holiday variables was combined with the member type, the difference in ride times were similar to the difference in ride times for just the two different member types. The correlations were relatively the same for the humidity, temperature, and wind speed when the member type was factored in. The exploratory analysis can be found in the files named [Data_Investigation.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Data_Investigation.ipynb) and [Data_Investigation_Report.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Data_Investigation_Report.ipynb).

### Statistical Findings
Through the use of confidence intervals and hypothesis testing, the observations from the graphical analysis were tested to determine if the differences in ride times happened through chance or if there actually was a difference in ride times. The tests used were the Student's t test and Analysis of Variance with a significance of 0.05 for all tests. For the t-tests done after ANOVA tests, the significance was reduced by dividing 0.05 by the number of additional tests. 
<br>
<br>
The ride times for the different rider types (Casual vs. Registered riders) was statistically significant (p-value ~= 0.0) and the difference was 14.13 minutes. From this analysis, it was also found that there were differences in ride times for the different seasons and there were differences within each season when member type was factored. The same conclusions were made for holiday/non-holidays, workdays/non-workdays, and the different weather categories. 
<br>
<br>

| Categories | Difference in Ride Times|
| --- | --- |
| Workday/Non-Workday | 3.18 minutes|
| Workday Casual vs. Registered | 12.62 minutes |
| Non-Workday Casual vs. Registered | 15.47 minutes |
| Holiday/Non-Holiday | 1.1 minutes |
| Holiday Casual vs. Registered | 14.15 minutes |
| Sunny Casual vs. Registered | 14.21 minutes |
| Cloudy/Misty Casual vs. Registered | 14.02 minutes |
| Rainy/Stormy/Snowy Casual vs. Registered | 8.74 minutes |
| Spring Casual vs. Registered | 14.92 minutes |
| Summer Casual vs. Registered | 14.47 minutes |
| Fall Casual vs. Registered | 13.0 minutes |
| Winter Casual vs. Registered | 13.68 minutes |
<br>
<br>
These tests also proved that there were correlations between wind speed, humidity, temperatures, and miles between the start and end stations. However, the correlation values for humidity (0.006), temperature (0.10), and wind speed (-0.02) were very low (close to 0), meaning that the correlation was not very large. The correlation was fairly large for ride time and miles between stations at 0.40. When the member type was factored in, the correlation between ride time and miles for registered riders was even larger (0.65). The correlation was relatively low when factoring in casual riders (0.14). The statistical inference notebook can be found in the file named [Bikeshare_Statistical_Inference.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Bikeshare_Statistical_Inference.ipynb).

### Conclusions
This analysis provided key insights as to which factors lead to increased or decreased ride times. The rider type seems to play a large role in the ride times. Season, weather, holidays, and workdays also play a role in how long a ride might take. When the rider type is factored into those four variables, the differences in ride times is very similar to the actual differences in ride times only between the rider type. These variables will more than likely be included the models that will be used to determine ride times. 
<br>
<br>
The tests also found that there was correlation between ride times and humidity, temperature, wind speed, and miles between two stations. While the temperature, humidity, and wind speed are not strongly correlated with the ride time, they will not be excluded from further analysis. This is because each variable was tested with the ride time variable but combinations of humidity, temperature, and wind speed were not looked at. Additional analysis will need to be done to determine if these variables affect the ride time. 

## Popular Stations
In addition to determining the influential variables in the data set, analysis was also done to determine the most popular locations where bike rides are started. An animated gif was created to show the hot spots for starting locations (contained in one of the zip files). Most of the rides are concentrated in major spots within the city: the White House, U Street neighborhood, Adams Morgan neighborhood, DuPont Circle, Logan Circle, the National Mall, Georgetown Waterfront, Union Station, and the Capitol Hill neighborhood. The gifs could not be included because the file sizes were too large for GitHub.
<br>
<br>
These locations make sense as these places are major areas where people live, where tourist attractions are, and where people go to socialize. Additional analysis with a dataset of more recent years could be used to compare the most popular stations between a newly built dataset and the dataset used in this analysis. The notebook that was used to create the gifs is named [Generate_Map_Gifs.ipynb](https://github.com/mcassi17/Bikeshare_Milestone1_Report/blob/master/Generate_Map_Gifs.ipynb). The gifs can be found on [Google Drive](https://drive.google.com/drive/folders/0B6_vx5KzXEc_cnNVM3prYVBDQ0k?usp=sharing).

## Next Steps
The next steps for this project is to begin looking at models to predict ride times between two stations. The data will need to be split into a training set and a test set so that the training set can be used to build the model and the test set can be used to determine if the model accurately predicts ride times. Additional data could also be included, if found, like traffic data. If this data is found, exploratory data analysis and statistical inference would be done to determine if that data influence ride times.
