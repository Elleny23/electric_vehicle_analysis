# Electric Vehicle Performance Analysis 

![image alt](https://github.com/Elleny23/electric_vehicle_analysis/blob/main/GNWm6AT4CMJn776hZJZETk9ugA99DI3OE9oB2VVy.jpg)

### Tools used : MySQL Server,Power BI

## Description:
With the global shift toward sustainable mobility, electric vehicles (EVs) have emerged as a key solution to reducing carbon emissions and dependence on fossil fuels. As EV adoption is growing rapidly understanding their performance, efficiency, and long-term viability becomes increasingly important. Therefore this project provides a comprehensive evaluation of electric vehicles (EVs) using SQL based analytics. The purpose of this analysis is to understand how factors such as battery capacity, charging time, energy consumption, and regional conditions influence overall EV performance and sustainability outcomes. By doing so the project aims to assist both consumers and industry stakeholders in identifying trends that define the next generation of sustainable mobility.

## Objectives:
- Understand factors affecting EV performance and cost.
- Identify top-performing manufacturers and models.
- Explore the relationship between speed, range, and efficiency.
- Assess CO₂ savings and environmental impact.

## Table of contents
- [Dataset used](#dataset)
- [SQL Analysis_code](#sql_analysis_code)
- [Power BI Dashboard-click to interact](#power_bi_dashboard)
- [README.md](#readme.md)
- [PPT](#ppt_file)
  

## Execution
#### Questions Answered from the Dataset

#### 1) What are the Key Performance Indicators obtained from the Dataset?

. Most Sold EV Company (by Vehicle Count)
```
SELECT 
Make, COUNT(Vehicle_ID) AS total_vehicles 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY total_vehicles DESC;

![image alt](<img width="375" height="307" alt="image" src="https://github.com/user-attachments/assets/c78588fe-546d-4f34-9d88-0c67fc368ab8" />)



. Average Battery Health (%)
```
SELECT Make, ROUND(AVG(`Battery_Health_%` ), 2) AS avg_battery_health 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY avg_battery_health DESC;


.Average Charging Time (hours)
```
SELECT Make, ROUND(AVG(Charging_Time_hr), 5) AS avg_charging_time_hr 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY avg_charging_time_hr ASC;




