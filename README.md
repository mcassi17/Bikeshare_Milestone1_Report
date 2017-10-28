# Capital Bikeshare Ride Time Prediction
## Problem and Question
Within the last few months, there has been an influx of bikesharing companies that have come into the Washington D.C. area. The most popular, Capital Bikeshare, provides all of their data online for free. With all of this available data, there is nothing out there to predict how long a ride might take while traveling between two Capital Bikeshare docking stations. The project will look to answer the question of how long will it take to ride between two stations. The prediction is for riders that want to know how long a ride might take based on time of day, day of week, weather, and ride distance.

## Data
Capital Bikeshare provides all of their rider data on their website, which includes the start time, start station, end time, end station, and member type (casual and registered). There are 4 datasets for each quarter of the year (Q2 from 2011, Q3 from 2011, Q4 from 2011, and Q1 from 2012). In addition to the bikeshare data, the University of California Irvine has a dataset that has weather data for 2011 and 2012 for bikeshare rides. This dataset contains information about the weather like temperature and humidity as well as information regarding holidays and workdays. The last dataset used in this initial report is the latitude and longitude data for each of the bikeshare stations from Open Data DC.
<br>
<br>
An additional dataset that could be useful for this analysis is traffic data. This could be another piece of information that could help determine how long a ride might take.

## Cleaning Data
The quarterly data and UCI weather dataset contain date columns that needed to be loaded in as datetime objects to make it easier for filtering and merging of the datasets. To handle the date columns, the partse_dates attributes were used on in the read_csv functions. This created all date columns as datetime objects in Python. There were not dates included in the latitude and longitude dataset.
<br>
<br>
When inspecting the first few rows of each dataframe, each datasets have columns that are not necessary for the analysis and prediction aspects of the project. The UCI weather dataset contains extra columns like month, year, and instant (an index column). The bikeshare dataset contains additional columns like duration (text field) and bike serial number. The latitude and longitude dataset contained extra columns about the different bikeshare docking stations outside of the latitudes and longitudes. All of these columns were removed as they would not impact the analysis to be performed. In addition to removing columns, some columns had to be renamed so all columns match, which is needed for the the joining of the datasets. 
<br>
<br>

There were not many missing values in each dataset. The bikeshare data contained 11 rows that did not have a final end point. These rows were removed from the bikeshare dataset. The UCI and lat/lon data did not have any null or missing values. 
<br>
<br>
One of the Bikeshare datasets contained negative values for the time difference column. After doing some research, this occurred because of daylight savings time. 60 minutes were added to the negative values to make adjust for the 1 hour difference. 
<br>
<br>
Prior to joining the datasets together, the date columns from all datasets needed to be in the same format. The UCI weather dataset date format is YYYY-MM-DD, while the Bikeshare datasets used a YYYY-MM-DD HH:MM:SS format. Two additional columns were created in the Bikeshare datasets with the YYYY-MM-DD format. The original columns with the hours, minutes, and seconds were kept. In addition to creating those two columns, another column was created for the time difference, in minutes, between the start and end times of the bikeshare dataset.

### Joining Datasets
Each quarterly dataset was joined with the UCI data using the start date in the bikeshare datasets and the date column from the UCI data. Once this was done, the 4 datasets were concatenated to create the full dataset of rides and UCI weather data. 
<br>
<br>
The next two joins were used to combine the latitudes and longitudes of all of the start and end stations for each ride. The first join was to combine the start station latitudes and longitudes with the full dataset. Once this was completed, the end station latitudes and longitudes were combined with the dataset. 

### Additional Data Cleaning
Once the datasets were all combined, there were some rows that did not have latitudes and longitudes, meaning these stations existed in 2011 and 2012 but no longer exist in 2017. These rows were removed from the dataset.
<br>
<br>
An additional column was created in the dataset, miles between the start and end station, using the latitudes and longitudes of the stations. The formula used to calculate this was the Vincenty distance from the geopy package in Python.

## Initial Findings

## Popular Stations

## Next Steps
