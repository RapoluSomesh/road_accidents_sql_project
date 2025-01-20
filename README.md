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

- **SQL File link:** [road_accident_sql_project](https://github.com/RapoluSomesh/road_accidents_sql_project/blob/main/road_accidents_sql_project.sql)

## Schema 

```sql
SELECT * FROM road_accidents.rta_dataset;
```
## General Insights

### 1.What is the total number of accidents recorded?

```sql
select count(*)as 'total no of accidents' from rta_dataset;
```
|Total no of accidents|
|-----|
|12316|
### 2.How many accidents occur each day of the week?
```sql
SELECT Day_of_week, COUNT(*) AS accident_count
FROM rta_dataset
GROUP BY Day_of_week
order by field(day_of_week,'monday', 'tuesday','wednesday','thursday','friday','saturday', 'sunday');
```
|Day of week|Accident count
|-----|----|
|Monday|	1681|
|Tuesday|	1770|
|Wednesday|	1840|
|Thursday|	1851|
|Friday|	2041|
|Saturday|	1666|
|Sunday|	1467|
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
|Time of day|Accidents count|
|---|---|
|Morning|	3312|
|Afternoon|	5125|
|Evening|	2669|
|Night|	1210|
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
|Types of vehicles |Accidents count|
|---|---|
|car|	4203|
|bus|	2458|
|truck	|3110|
|other|	2242|
|motorcycle|	206|
|bicycle|	21|
## Driver & Vehicle Analysis
### 5.What is the most common age group of drivers involved in accidents?
```sql
select Age_band_of_driver, count(*) as 'accident_count' from rta_dataset group by Age_band_of_driver
order by field(Age_band_of_driver, 'under 18', '18-30', '31-50','over 51','unknown');
```
|Age band of driver|Accident count|
|-|-|
|Under 18	|825|
|18-30	|4271|
|31-50	|4087|
|Over 51	|1585|
|Unknown|	1548|
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
|Driving experience|Accident count|
|-|-|
|Below 1year|	1342|
|1-2years|	1756|
|2-5years|	2613|
|5-10years|	3363|
|Above 10years	|2262|
|No Licence	|118|
|unknown|	862|
### 7.What are the top causes of accidents involving different vehicle types?
```sql
select Cause_of_accident,types_of_vehicle, count(*) as 'accident_count'  
from rta_dataset where Types_of_vehicle is not null 
group by Cause_of_accident, Types_of_vehicle
order by Cause_of_accident, accident_count desc ;
```

|Causes of accidents|types of vehicles |Accident count|
|-|-|-|
|Changing lane to the left|	car|	503|
|Changing lane to the left|	truck|	364|
|Changing lane to the left|	bus|	298|
|Changing lane to the left|	other|	280|
|Changing lane to the left|	motorcycle|	20|
Changing lane to the left|	bicycle|	2|
Changing lane to the right|	car|	610
Changing lane to the right|	truck|	461
Changing lane to the right|	bus|	366
Changing lane to the right|	other|	336
Changing lane to the right|	motorcycle|	24
Changing lane to the right|	bicycle|	3
Driving at high speed|	car|	56
Driving at high speed|	bus|	47
Driving at high speed|	truck|	36
Driving at high speed|	other|	31
Driving at high speed|	motorcycle|	2
Driving carelessly|	car|	498
Driving carelessly|	truck|	343
Driving carelessly|	bus|	274
Driving carelessly|	other|	250
Driving carelessly|	motorcycle|	27
Driving carelessly|	bicycle|	3
Driving to the left|	car	|105
Driving to the left|	truck	|71
Driving to the left|	other|	55
Driving to the left|	bus|	51
Driving to the left|	motorcycle|	2
Driving under the influence of drugs|	car	|113
Driving under the influence of drugs|	truck|	88
Driving under the influence of drugs|	bus|	72
Driving under the influence of drugs|	other|59
Driving under the influence of drugs|	motorcycle|	5
Driving under the influence of drugs|	bicycle|	1
Drunk driving|	truck|	11
Drunk driving|	car|	10
Drunk driving|	bus|	4
Drunk driving|	other|	2
Getting off the vehicle improperly|	car|	85
Getting off the vehicle improperly|	truck|	43
Getting off the vehicle improperly|	other	|37
Getting off the vehicle improperly|	bus	|27
Getting off the vehicle improperly|	motorcycle|	4
Improper parking|	car	|13
Improper parking|	other|	5
Improper parking|	truck|	4
Improper parking|	bus|	3
Moving Backward	|car|	381
Moving Backward|	truck|	276
Moving Backward	|bus|	246
Moving Backward	|other|	214
Moving Backward	|motorcycle|	15
Moving Backward	|bicycle|	1
No distancing|	car	|762
No distancing	|truck|	580
No distancing|	other|	423
No distancing|	bus|	422
No distancing|motorcycle|	51
No distancing|	bicycle	|4
No priority to pedestrian|	car|	248
No priority to pedestrian|	truck|	181
No priority to pedestrian|	bus|	154
No priority to pedestrian|	other|	124
No priority to pedestrian|	motorcycle|	11
No priority to vehicle|	car	|389
No priority to vehicle|	truck	|329
No priority to vehicle|	bus|	245
No priority to vehicle|	other|	213
No priority to vehicle|	motorcycle|	18
No priority to vehicle|	bicycle|	5
Other|	car|	156
Other|	bus|	100
Other|	truck	|94
Other|	other|	87
Other|	motorcycle|	11
Other|	bicycle|	1
Overloading|	truck|	19
Overloading|	car|	16
Overloading|	bus|	14
Overloading|	other|	9
Overloading|	motorcycle|	1
Overspeed|	car|	21
Overspeed|	truck|	18
Overspeed|	other	|14
Overspeed	|bus|	8
Overtaking|	car|	154
Overtaking|	truck	|114
Overtaking|	bus|	84
Overtaking	|other	|59
Overtaking|	motorcycle|	13
Overturning|	truck|	55
Overturning|	car|	51
Overturning|	bus|	23
Overturning|	other|	19
Turnover|	car|	21
Turnover|	truck|	19
Turnover|	other|	18
Turnover|	bus|	17
Turnover|	motorcycle|	2
Turnover|	bicycle|	1
Unknown|	car|	11
Unknown|	other|	7
Unknown	|truck|	4
|Unknown|	bus|	3|

## Casualties & Severity:
### 8.What is the breakdown of accidents by severity (e.g., slight, serious)?
```sql
select Accident_severity, count(*) as 'accident_count' 
from rta_dataset 
group by Accident_severity;
```
|Accident severity|Accident count|
|-|-|
|Slight Injury|	10415|
Serious Injury|	1743
|Fatal injury|	158|

### 9.many casualties are reported per accident on average?
```sql
select avg(Number_of_casualties) as average_casualties_per_accident from rta_dataset;
```
|Average casualties per accident|
|-|
|1.5481|
### 10.What is the correlation between weather conditions and accident severity?
```sql
select Weather_conditions, Accident_severity from rta_dataset 
group by Weather_conditions, Accident_severity
order by Accident_severity;
```
|Weather condition|Accident severity|
|-|-|
|Normal|	Fatal injury
Raining|	Fatal injury
Normal|	Serious Injury
Raining|	Serious Injury
Unknown|	SeriousInjury
Windy|	Serious Injury
Other|	Serious Injury
Snow|	Serious Injury
Cloudy|	Serious Injury
Fog or mist|	Serious Injury
Raining and Windy|	Serious Injury
Normal|	Slight Injury
Raining	|Slight Injury
Raining and Windy|	Slight Injury
Cloudy|	Slight Injury
Other|	Slight Injury
Windy|	Slight Injury
Snow|	Slight Injury
Fog or mist|	Slight Injury
Unknown|	Slight Injury
### 11.What are the most common injuries associated with different types of collisions?
```sql
select Type_of_collision, count(*) as 'accident_count' 
from rta_dataset 
where Type_of_collision <> '' 
group by Type_of_collision 
order by accident_count desc;
```
|Type of collision|Accident count|
|-|-|
|Vehicle with vehicle collision|	8774|
Collision with roadside objects|	1786
Collision with pedestrians|	896
Rollover|	397
Collision with animals|	171
Collision with roadside-parked vehicles|	54
Fall from vehicles|	34
Other|	26
Unknown|	14
|With Train|	9|

## Location & Conditions
### 12.Which areas have the highest number of accidents?
```sql
select Area_accident_occured, count(*)as 'accident_count' 
from rta_dataset 
where Area_accident_occured <> '' 
group by Area_accident_occured 
order by accident_count desc;
```
|Area accident occured|Accident count|
|-|-|
|Other|	3819|
Office areas|	3451
Residential areas|	2060
 Church areas|	1060
 Industrial areas|	456
School areas|	415
  Recreational areas|	327
 Outside rural areas|	218
 Hospital areas|	121
  Market areas|	63
Rural village areas|	44
Unknown|	22
Rural village areasOffice areas|	20
|Recreational areas|	1|

### 13. How do road surface conditions impact accident frequency?
```sql
select Road_surface_conditions, Accident_severity,count(*)as 'accident_count' 
from rta_dataset group by Road_surface_conditions,Accident_severity
order by Accident_severity desc;
```
|Road surface condition|Accident severity|Accident count|
|-|-|-|
|Dry|	Slight Injury|	7887|
Wet or damp|	Slight Injury|	2460
Snow	|Slight Injury|	66
Flood over 3cm. deep|	Slight Injury|	2
Dry	|Serious Injury	|1332
Wet or damp|	Serious Injury|	407
Snow|	Serious Injury|	4
Dry|	Fatal injury|	121
Wet or damp|	Fatal injury|	37

### 14.What is the effect of light conditions (day/night) on accident severity?
```sql
UPDATE rta_dataset
SET Light_conditions = 'Darkness - lights unlit'
WHERE Light_conditions = 'Darkness - lights lit';
select Light_conditions,Accident_severity,count(*)as 'accident_count' from rta_dataset group by Light_conditions,Accident_severity
order by Accident_severity desc;
```
|Light condition|Accident severity|Accident count|
|-|-|-|
|Daylight|	Slight Injury|	7489|
Darkness - lights unlit|	Slight Injury|	2788
Darkness - no lighting|	Slight Injury|	138
Daylight|	Serious Injury|	1222
Darkness - lights unlit|	Serious Injury|	472
Darkness - no lighting|	Serious Injury|	49
Daylight|	Fatal injury|	87
Darkness - lights unlit|	Fatal injury|	66
|Darkness - no lighting|	Fatal injury|	5|

# Conclusion

The project aims to provide actionable insights into accident trends, contributing factors, and preventive measures. Key conclusions might include identifying high-risk times, common vehicle and driver profiles, and environmental factors to guide policymakers, transportation authorities, and the general public in reducing accident rates.
