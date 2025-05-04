# Cyclistic Data Analysis

## 1. Business Task

Cyclistic, a Chicago-based bike-share company, wants to analyze customer behavior to understand how annual members and casual riders use its bikes differently. The goal is to help Cyclistic’s marketing team create data-driven strategies to convert more casual users into members.

## 2. Data Source

The analysis uses a merged dataset called all_trips, combining 12 months of ride data (CSV format) imported into MySQL. The dataset includes:

ride_id, rideable_type, start and end time

station names and coordinates

member type (member/casual)

## 3. Data Cleaning (via SQL)

### SQL queries were used in MySQL Workbench to

#### _-- Remove rows with NULLs or incorrect ride times_
DELETE FROM all_trips
WHERE started_at IS NULL OR ended_at IS NULL
   OR ended_at <= started_at;

#### _-- Add ride duration in minutes_
ALTER TABLE all_trips ADD ride_length INT;
UPDATE all_trips
SET ride_length = TIMESTAMPDIFF(MINUTE, started_at, ended_at);

#### _-- Add weekday for each trip_
ALTER TABLE all_trips ADD day_of_week VARCHAR(20);
UPDATE all_trips SET day_of_week = DAYNAME(started_at);

#### _-- Remove outliers: rides < 1 or > 1440 minutes (24 hours)_
DELETE FROM all_trips
WHERE ride_length < 1 OR ride_length > 1440;

#### Data was confirmed to have only member and casual in member_casual after cleaning.

## 4. Analysis Queries (via SQL)

#### _-- Total rides by user type_
SELECT member_casual, COUNT(*) AS total_rides
FROM all_trips
GROUP BY member_casual;

![Image](https://github.com/user-attachments/assets/b47c5557-14a1-4394-8ab1-67df55f7629f)

#### _-- Average ride duration by user type_
SELECT member_casual, ROUND(AVG(ride_length), 2) AS avg_ride_length
FROM all_trips
GROUP BY member_casual;

#### _-- Rides by day of week_
SELECT day_of_week, member_casual, COUNT(*) AS total_rides
FROM all_trips
GROUP BY day_of_week, member_casual
ORDER BY FIELD(day_of_week, 'Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday');

![Image](https://github.com/user-attachments/assets/d14547b5-2b8c-4997-bdad-ade87c3b3af5)

#### _-- Average ride length by day and user type_
SELECT day_of_week, member_casual, ROUND(AVG(ride_length), 2) AS avg_ride_length
FROM all_trips
GROUP BY day_of_week, member_casual
ORDER BY FIELD(day_of_week, 'Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday');

![Image](https://github.com/user-attachments/assets/f4dc7258-3cb3-4024-b160-b8770323f587)

#### _-- Top 5 start stations_
SELECT start_station_name, COUNT(*) AS rides
FROM all_trips
WHERE start_station_name IS NOT NULL
GROUP BY start_station_name
ORDER BY rides DESC
LIMIT 5;

![Image](https://github.com/user-attachments/assets/94406ed0-0eec-4e4a-abf6-b489a21016a9)

#### _-- Top 5 end stations_
SELECT end_station_name, COUNT(*) AS rides
FROM all_trips
WHERE end_station_name IS NOT NULL
GROUP BY end_station_name
ORDER BY rides DESC
LIMIT 5;

![Image](https://github.com/user-attachments/assets/92b05fe7-81a0-48cf-a901-dcf03f7365a2)

#### _-- Bike type usage_
SELECT rideable_type, member_casual, COUNT(*) AS ride_count
FROM all_trips
GROUP BY rideable_type, member_casual;

![Image](https://github.com/user-attachments/assets/96785f6d-5dc0-4a63-a1d7-179db6e90c8b)

## 5. Summary of Key Findings

Members take more total rides, but casual riders take longer rides on average.

Casual riders prefer weekends while members ride more during weekdays.

Both segments prefer classic bikes, but casual users use more electric and docked bikes.

Top stations are highly used by casual riders, likely in tourist-heavy areas.

## 6. Recommendations

Launch weekend ride promotions for casual users.

Expand bike stations near top start/end locations.

Create commuter-focused perks for members (e.g. faster checkout, loyalty).

## 7. Tools Used

MySQL Workbench (Data Cleaning & Queries)

Excel (Charts & Final Report)

