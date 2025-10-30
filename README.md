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

Most Sold EV Company (by Vehicle Count)
```
SELECT 
Make, COUNT(Vehicle_ID) AS total_vehicles 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY total_vehicles DESC;
```




Average Battery Health (%)
```
SELECT Make, ROUND(AVG(`Battery_Health_%` ), 2) AS avg_battery_health 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY avg_battery_health DESC;
```

Average Charging Time (hours)
```
SELECT Make, ROUND(AVG(Charging_Time_hr), 5) AS avg_charging_time_hr 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY avg_charging_time_hr ASC;
```

Total CO₂ Saved (tons)
```
SELECT Make, ROUND(SUM(CO2_Saved_tons), 2) AS total_co2_saved_tons 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY total_co2_saved_tons DESC;
```

#### 2) Which electric vehicle models are the most battery-efficient (range per kWh), and how do manufacturers compare?
```
SELECT 
model,
make, 
AVG(range_km / battery_capacity_kwh) AS battery_efficiency_km_per_kwh
FROM rizdb.electric_vehicle_analytics
GROUP BY make,model 
ORDER BY battery_efficiency_km_per_kwh DESC;
```


#### 3) Which regions have the lowest monthly charging cost per 1,000 km driven?
```
WITH cost_normalized AS (SELECT 
Region,(Monthly_Charging_Cost_USD / NULLIF(Mileage_km, 0)) * 1000 AS cost_per_1000km
FROM rizdb.electric_vehicle_analytics)
SELECT 
Region,
AVG(cost_per_1000km) AS AVG_cost_per_1000km
FROM cost_normalized
GROUP BY Region
ORDER BY AVG_cost_per_1000km ASC;
```


#### 4) How does average battery health (%) change with number of charge cycles across usage types (Personal vs. Commercial)?
```
SELECT 
    FLOOR(charge_cycles/100)*100 AS cycle_bin,
    ROUND(AVG(CASE WHEN usage_type = 'Personal' THEN `Battery_Health_%` END), 2) AS personal_avg_battery,
    ROUND(AVG(CASE WHEN usage_type = 'Commercial' THEN `Battery_Health_%` END), 2) AS commercial_avg_battery,
    ROUND(AVG(CASE WHEN usage_type = 'Fleet' THEN `Battery_Health_%` END), 2) AS fleet_avg_battery
FROM rizdb.electric_vehicle_analytics
GROUP BY cycle_bin
ORDER BY cycle_bin;
```


#### 5) Rank vehicles by resale value relative to total mileage and battery health. Which usage factors explain the highest resale value?
```
SELECT 
    Make,
    Model,
    ROUND(AVG(resale_value_usd), 2) AS avg_resale_value_usd,
    ROUND(AVG(mileage_km), 0) AS avg_mileage_km,
    ROUND(AVG(`Battery_Health_%`), 1) AS avg_battery_health
FROM rizdb.electric_vehicle_analytics
GROUP BY Make, Model
ORDER BY avg_resale_value_usd DESC;
```


#### 6) Which manufacturer provides the highest CO₂ savings per $1,000 spent on maintenance?
```
SELECT make, 
AVG((co2_saved_tons / NULLIF(maintenance_cost_usd, 0 )) * 1000) AS Co2_savings_per_1000_maintenance
FROM rizdb.electric_vehicle_analytics
GROUP BY make 
ORDER BY Co2_savings_per_1000_maintenance DESC;
```


#### 7) Do higher max-speed vehicles tend to consume more energy (kWh/100km)? Find the correlation grouped by vehicle type.
```
SELECT 
    vehicle_type,
    (
        AVG(max_speed_kmh * energy_consumption_kwh_per_100km)
        - AVG(max_speed_kmh) * AVG(energy_consumption_kwh_per_100km)
    ) /
    (STDDEV_POP(max_speed_kmh) * STDDEV_POP(energy_consumption_kwh_per_100km))
    AS speed_energy_corr
FROM rizdb.electric_vehicle_analytics
GROUP BY vehicle_type
ORDER BY speed_energy_corr DESC;
```


#### 8) Which models achieve the fastest charging time relative to their charging power (kWh per hour)?
```
SELECT 
  make,
  model,
ROUND(AVG(charging_time_hr / battery_capacity_kwh), 5) AS charging_performace
FROM rizdb.electric_vehicle_analytics
GROUP BY make, model
ORDER BY charging_performace ASC
LIMIT 10;
```


#### 9) Which vehicle types have the highest insurance cost relative to max speed and acceleration?
```
WITH vehicle_type AS (SELECT
vehicle_type,
AVG(insurance_cost_usd / NULLIF(max_speed_kmh, 0)) AS cost_per_maxspeed,
AVG(insurance_cost_usd / NULLIF(acceleration_0_100_kmh_sec, 0)) AS cost_per_acceleration
FROM rizdb.electric_vehicle_analytics eva 
GROUP BY vehicle_type)
SELECT 
vehicle_type,cost_per_maxspeed, cost_per_acceleration
FROM vehicle_type
ORDER BY cost_per_maxspeed DESC, cost_per_acceleration DESC; 
```



#### 10) How does average regional temperature affect battery range (km)?
```
SELECT region,
CASE
WHEN temperature_c < 10 THEN 'Cold'
WHEN temperature_c BETWEEN 10 AND 25 THEN 'Moderate'
ELSE 'Hot'
END AS reg_temperature,
AVG(range_km) AS battery_range_km
FROM rizdb.electric_vehicle_analytics 
GROUP BY region,reg_temperature
ORDER BY region, reg_temperature;
```
