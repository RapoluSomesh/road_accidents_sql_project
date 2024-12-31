# Road Accidents Data Analysis using SQL
![road accident logo](https://github.com/RapoluSomesh/road_accidents_sql_project/blob/main/road_accident_image.jpg)

## Objectives

### General Insights:

- What is the total number of accidents recorded?
- How many accidents occur each day of the week?
- What is the distribution of accidents by time of day (e.g., morning, afternoon, evening)?
- Which type of vehicles are most frequently involved in accidents?

### Driver & Vehicle Analysis:

- What is the most common age group of drivers involved in accidents?
- How does driving experience affect the likelihood of being involved in an accident?
- What are the top causes of accidents involving different vehicle types?

### Casualties & Severity:

- What is the breakdown of accidents by severity (e.g., slight, serious)?
- many casualties are reported per accident on average?
- What is the correlation between weather conditions and accident severity?
- What are the most common injuries associated with different types of collisions?

### Location & Conditions:

- Which areas have the highest number of accidents?
- How do road surface conditions impact accident frequency?
- What is the effect of light conditions (day/night) on accident severity?

## Dataset

The data for this project is sourced from the kaggle dataset:

- **Dataset Link:** [road_accident_dataset](https://github.com/RapoluSomesh/road_accidents_sql_project/blob/main/rta_dataset.csv)

## Schema 

```sql
SELECT * FROM road_accidents.rta_dataset;
```
## General Insights

### 1.What is the total number of accidents recorded?

```sql
select count(*)as 'total no of accidents' from rta_dataset;
```
### 2.How many accidents occur each day of the week?
```sql
SELECT Day_of_week, COUNT(*) AS accident_count
FROM rta_dataset
GROUP BY Day_of_week
order by field(day_of_week,'monday', 'tuesday','wednesday','thursday','friday','saturday', 'sunday');
```
### 3. What is the distribution of accidents by time of day (e.g., morning, afternoon, evening)?
```sql
ALTER TABLE rta_dataset ADD time_of_day VARCHAR(10);
SET SQL_SAFE_UPDATES = 0;
UPDATE rta_dataset
SET time_of_day = CASE
    WHEN TIME(time) BETWEEN '05:00:00' AND '11:59:59' THEN 'Morning'
    WHEN TIME(time) BETWEEN '12:00:00' AND '17:59:59' THEN 'Afternoon'
    WHEN TIME(time) BETWEEN '18:00:00' AND '21:59:59' THEN 'Evening'
    ELSE 'Night'
END;
ALTER TABLE rta_dataset
MODIFY time_of_day VARCHAR(20);

select time_of_day,count(*) as 'accidents_count' 
from rta_dataset
group by time_of_day
order by field(time_of_day,'Morning','Afternoon','Evening','Night');
```
### 4. Which type of vehicles are most frequently involved in accidents?
```sql
update rta_dataset 
set types_of_vehicle = case
	when Type_of_vehicle='Automobile' then 'car'
	when Type_of_vehicle='Taxi' then 'car'
    when Type_of_vehicle='Stationwagen' then 'car'
    when Type_of_vehicle='Turbo' then 'car'
    when Type_of_vehicle='Public (> 45 seats)' then 'bus'
    when Type_of_vehicle='Public (13?45 seats)' then 'bus'
    when Type_of_vehicle='Public (12 seats)' then 'bus'
    when Type_of_vehicle='Pick up upto 10Q' then 'bus'
    when Type_of_vehicle='Lorry (41?100Q)' then 'truck'
    when Type_of_vehicle='Lorry (11?40Q)' then 'truck'
    when Type_of_vehicle='Long lorry' then 'truck'
    when Type_of_vehicle='Bajaj' then 'motorcycle'
    when Type_of_vehicle='Motorcycle' then 'motorcycle'
    when Type_of_vehicle='Special vehicle' then 'other'
    when Type_of_vehicle='Other' then 'other'
    when Type_of_vehicle='' then 'other'
    when Type_of_vehicle='Bicycle' then 'bicycle'
end;
select types_of_vehicle  ,count(*) as 'accident_count' from rta_dataset
where types_of_vehicle is not null
group by types_of_vehicle ;
```
## Driver & Vehicle Analysis
### 5.What is the most common age group of drivers involved in accidents?
```sql
select Age_band_of_driver, count(*) as 'accident_count' from rta_dataset group by Age_band_of_driver
order by field(Age_band_of_driver, 'under 18', '18-30', '31-50','over 51','unknown');
```
### 6.How does driving experience affect the likelihood of being involved in an accident?
```sql
update rta_dataset
set driving__experience = 
case
	when Driving_experience = '1-2yr' then '1-2years'
    when Driving_experience = 'Above 10yr' then 'Above 10years'
    when Driving_experience = '5-10yr' then '5-10years'
	when Driving_experience = '2-5yr' then '2-5years'
    when Driving_experience = 'No Licence' then 'No Licence'
    when Driving_experience = 'Below 1yr' then 'Below 1year'
    when Driving_experience = 'unknown' then 'unknown'
    when Driving_experience = '' then 'unknown'    
end;
select driving__experience, count(*) as 'accident_count' 
from rta_dataset group by driving__experience
order by field(driving__experience,'Below 1year','1-2years','2-5years','5-10years','Above 10years','No Licence','unknown');
```
### 7.What are the top causes of accidents involving different vehicle types?
```sql
select Cause_of_accident,types_of_vehicle, count(*) as 'accident_count'  
from rta_dataset where Types_of_vehicle is not null 
group by Cause_of_accident, Types_of_vehicle
order by Cause_of_accident, accident_count desc ;
```
## Casualties & Severity:
### 8.What is the breakdown of accidents by severity (e.g., slight, serious)?
```sql
select Accident_severity, count(*) as 'accident_count' 
from rta_dataset 
group by Accident_severity;
```
### 9.many casualties are reported per accident on average?
```sql
select avg(Number_of_casualties) as average_casualties_per_accident from rta_dataset;
```
### 10.What is the correlation between weather conditions and accident severity?
```sql
select Weather_conditions, Accident_severity from rta_dataset 
group by Weather_conditions, Accident_severity
order by Accident_severity;
```
### 11.What are the most common injuries associated with different types of collisions?
```sql
select Type_of_collision, count(*) as 'accident_count' 
from rta_dataset 
where Type_of_collision <> '' 
group by Type_of_collision 
order by accident_count desc;
```
## Location & Conditions
### 12.Which areas have the highest number of accidents?
```sql
select Area_accident_occured, count(*)as 'accident_count' 
from rta_dataset 
where Area_accident_occured <> '' 
group by Area_accident_occured 
order by accident_count desc;
```
### 13. How do road surface conditions impact accident frequency?
```sql
select Road_surface_conditions, Accident_severity,count(*)as 'accident_count' 
from rta_dataset group by Road_surface_conditions,Accident_severity
order by Accident_severity desc;
```
### 14.What is the effect of light conditions (day/night) on accident severity?
```sql
UPDATE rta_dataset
SET Light_conditions = 'Darkness - lights unlit'
WHERE Light_conditions = 'Darkness - lights lit';
select Light_conditions,Accident_severity,count(*)as 'accident_count' from rta_dataset group by Light_conditions,Accident_severity
order by Accident_severity desc;
```

# Conclusion

The project aims to provide actionable insights into accident trends, contributing factors, and preventive measures. Key conclusions might include identifying high-risk times, common vehicle and driver profiles, and environmental factors to guide policymakers, transportation authorities, and the general public in reducing accident rates.
