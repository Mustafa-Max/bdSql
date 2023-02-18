# bdSql
### I utilized SQL (SMSS) to analyze massive data from four tables, combining them and analyzing duration time and weekday.
---
#import data to SSMS or any applactions you are going to use NOTE(I will use SSMS)
1. Create a datebase 
2. download data from this [Divvy_trips_Quarter](https://www.kaggle.com/datasets/malhaddi/divvy-trips) ,For monthly if you want [here](https://divvy-tripdata.s3.amazonaws.com/index.html)
3. Import tables inside database 
---
# First Query
### Change type of data column to make to make sure combine query working without any issue.

```
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN ride_id nvarchar(50);
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN started_at datetime2(7);
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN ended_at datetime2(7);
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN rideable_type nvarchar(50);
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN start_station_id smallint;
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN start_station_name nvarchar(50);
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN end_station_id smallint;
ALTER TABLE dbo.Divvy_trips_2019_Q4
ALTER COLUMN end_station_name nvarchar(50);
```
# Second Query 
### Will combine 4 tables all in one to make it easy to analyse

```
SELECT ride_id, started_at, ended_at, rideable_type, start_station_id, start_station_name, end_station_id, end_station_name, member_casual
FROM dbo.Divvy_trips_2019_Q2

UNION ALL
SELECT ride_id, started_at, ended_at, rideable_type, start_station_id, start_station_name, end_station_id, end_station_name, member_casual
FROM dbo.Divvy_trips_2019_Q3

UNION ALL
SELECT ride_id, started_at, ended_at, rideable_type, start_station_id, start_station_name, end_station_id, end_station_name , member_casual
FROM dbo.Divvy_trips_2019_Q4

UNION ALL
SELECT ride_id, started_at, ended_at, rideable_type, start_station_id, start_station_name, end_station_id, end_station_name , member_casual
FROM dbo.Divvy_trips_2020_Q1;
```
1. Right-click on the result set and select "Save Results As" from the context menu.
2. In the "Save Results" dialog box, choose the location where you want to save the file and select the file type you want to export to (e.g., CSV, Excel, etc.).(CSV Better)
3. Click the "Options" button to customize the export settings. You can choose which columns to include in the export, set field delimiters, and more.
4. Click the "OK" button to close the Options dialog box.
5. Click the "Save" AS ***all_trips_19Q2_20Q1*** button to export the data.
6. Import exported ***all_trips_19Q2_20Q1.csv***  file to SSMS 
---

# Third Query
```
ALTER TABLE all_trips_19Q2_20Q1
ADD duration_time nvarchar(50),
ADD day_of_week nvarchar(50);
ADD ride_length nvarchar(50);
```
- The names of the columns will be used to add data that we will analyze.
- nvarchar(50) data type we will use for these columns.
---

# Fourth Query
```
UPDATE all_trips_19Q2_20Q1
SET duration_time = CONVERT(varchar(15), DATEADD(second, DATEDIFF(second, CAST(started_at AS datetime), CAST(ended_at AS datetime)), 0), 114);
```
- calculated the duration time from started to ended (started_at, ended_at)
- he 0 in the DATEADD function is the number of intervals that should be added to the start_time to get the desired result. In this case, the second parameter is the difference between the start_time and end_time, which is in seconds. So setting the 0 parameter means that the result will be a time value that represents the number of seconds in the duration of the trip.
- The 114 in the CONVERT function specifies the output format of the time value. In this case, 114 specifies the "hh:mi:ss" format, where hh represents hours (00-23), mi represents minutes (00-59), and ss represents seconds (00-59). So the resulting duration value will be a string in the format "hh:mm:ss".
---

# Fifth Query
```
UPDATE all_trips_19Q2_20Q1
SET day_of_week = DATENAME(dw, ended_at)
```
- The DATENAME function in SQL Server returns a string that represents the specified datepart of a given date. In this case, the dw parameter is used to specify that the day of the week should be returned as a string. The day_of_week column in the all_trips_19Q2_20Q1 table is being set to the name of the day of the week on which each trip ended.
- For example, if a trip ended on a Monday, the value of the day_of_week column for that trip would be set to the string "Monday". The day of the week is represented as a two-character abbreviation by default (e.g. "Mo" for Monday), but by using the DATENAME function with the dw parameter, the full name of the day of the week can be obtained.
---

# Sixth Query
```
UPDATE all_trips_19Q2_20Q1
SET ride_length = DATEDIFF(second, started_at, ended_at)
````
- calculated seconds from started_at to end_at
---

# Seventh Query
```
UPDATE all_trips_19Q2_20Q1
	SET member_casual =
		CASE
		  WHEN member_casual = 'Subscriber' THEN 'member'
		  WHEN member_casual = 'Customer' THEN 'casual'
		  ELSE member_casual
		END;
```
- member_casual columns have 4 types to make it easy to analyse converte Subscriber|member and Customer|casual
---

# Nineth Query
```
SELECT day_of_week, member_casual, COUNT(*) AS count_riders, AVG(DATEDIFF(second, started_at, ended_at)) AS avg_ride_length
FROM all_trips_19Q2_20Q1
GROUP BY day_of_week, member_casual
```
- Create a table with the number of riders and the average rider length for each day of the week.
- Import the results to CSV FILE named ***trips_per_day*** **Optional**

---
# Tenth Query
```
SELECT *
FROM all_trips_19Q2_20Q1
```
- for data Visualization (tableau)
- Import the results to CSV FILE named ***all_trips_v2_19Q2_20Q1*** Optional
---
# Data Visualization 
## I used tablea You review it and give me feedback [Here](https://public.tableau.com/views/CaseStudyCyclisticBikersServices/Dashboard1?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link)
---
## Many thanks to the Google course instructors and the outstanding data analysts who offer data for practice.
