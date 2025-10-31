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
![image alt]()

This table shows the total number of EVs sold by each manufacturer. Ford leads with 323 vehicles, followed closely by BMW (311) and Volkswagen (308). This suggests strong brand penetration and consumer trust in Ford’s EV lineup. High sales volumes also reflect broader dealership networks, competitive pricing, and effective marketing strategies.

Average Battery Health (%)
```
SELECT Make, ROUND(AVG(`Battery_Health_%`),2) AS avg_battery_health 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY avg_battery_health DESC;
```
Tesla ranks highest with an average battery health of 85.95%, outperforming all other brands. Ford (85.3%) and BMW (85.27%) follow closely, indicating competitive battery management systems. This metric represents how well EV batteries retain their capacity over time, a key indicator of efficiency and reliability.


Average Charging Time (hours)
```
SELECT Make, ROUND(AVG(Charging_Time_hr), 5) AS avg_charging_time_hr 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY avg_charging_time_hr ASC;
```
Tesla again leads with the fastest charging time (1.09 hours), showing high efficiency in energy transfer and optimized charging infrastructure. Nissan (1.15 hrs) and Audi (1.16 hrs) also perform well, while Kia (1.30 hrs) ranks lowest. Lower charging times improve user convenience and support wider EV adoption.

Total CO₂ Saved (tons)
```
SELECT Make, ROUND(SUM(CO2_Saved_tons), 2) AS total_co2_saved_tons 
FROM rizdb.electric_vehicle_analytics 
GROUP BY Make 
ORDER BY total_co2_saved_tons DESC;
```
In terms of environmental impact, Volkswagen leads by saving approximately 4,930 tons of CO₂, followed by BMW (4,741 tons) and Hyundai (4,696 tons). These savings reflect the combined effect of higher vehicle efficiency, sales volume, and distance covered by EVs.


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
The Hyundai Kona Electric ranks first with a battery efficiency of 5.05 km/kWh, making it the most energy-efficient model in this dataset. Other top performers include Audi e-tron (5.04 km/kWh) and Ford Mustang Mach-E (5.03 km/kWh). Tesla’s models maintain consistent performance around 4.9–4.95 km/kWh, showcasing balanced power and range optimization.

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
This query compares the average cost of operating EVs per 1,000 km across different regions.
Australia shows the lowest cost (≈3.28 units), suggesting cheaper electricity rates or higher charging efficiency.
Europe (3.36) and Asia (3.42) record slightly higher costs, possibly due to differences in grid energy pricing and charging infrastructure.
EV operating costs remain fairly consistent globally, but regions with renewable power and better infrastructure, like Australia, achieve lower per-kilometer costs.

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
This output analyzes battery degradation trends across different vehicle types over charge cycles.
Up to 700 cycles, battery health remains stable (~85–86%), after which a gradual decline is visible.
Commercial EVs show slightly higher degradation due to more intensive usage.
Personal EVs maintain steadier performance, while fleet batteries exhibit wider fluctuations from shared or long-duration charging.
Analyst Note:
Battery health declines moderately after 1,000 cycles, highlighting the need for balanced charging practices and efficient energy management systems.

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
This table identifies models with high resale value and battery retention.
BMW i4 ($23,530) has the highest resale value, supported by strong battery performance (85.4%).
Tesla Model S offers balanced results—slightly lower resale value but excellent battery health (85.9%).
Hyundai Ioniq 5 and Kia Niro EV demonstrate reliability with good mileage and stable battery metrics.
Analyst Note:
Premium EVs like BMW and Tesla maintain high resale potential due to superior battery technology and consumer trust in brand performance.

#### 6) Which manufacturer provides the highest CO₂ savings per $1,000 spent on maintenance?
```
SELECT make, 
AVG((co2_saved_tons / NULLIF(maintenance_cost_usd, 0 )) * 1000) AS Co2_savings_per_1000_maintenance
FROM rizdb.electric_vehicle_analytics
GROUP BY make 
ORDER BY Co2_savings_per_1000_maintenance DESC;
```
This analysis links maintenance efficiency with environmental impact.
Hyundai leads with 21.45 tons of CO₂ saved, followed by Nissan (20.11) and Volkswagen (19.79).
This suggests their EVs combine durable components with reduced maintenance emissions.
Luxury brands (Audi, Mercedes) show lower CO₂ savings, possibly due to heavier vehicle weight and energy-intensive parts.
Analyst Note:
Hyundai and Nissan demonstrate strong environmental efficiency, reducing carbon footprint through optimized maintenance and energy systems.

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
This output measures how vehicle speed correlates with energy consumption.
Hatchbacks (0.06) show a slight positive correlation, meaning higher speeds increase energy use marginally.
Trucks also show weak positive correlation due to heavier loads.
Sedans and SUVs exhibit negative correlations, suggesting better energy optimization at moderate speeds.
Analyst Note:
The weak correlations indicate that EV energy efficiency depends more on aerodynamics and driving patterns than on speed alone.

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
This analysis identifies models with the most efficient charging-to-range ratio.
Tesla Model Y (0.01265) and Model 3 (0.01401) lead the ranking, reflecting Tesla’s superior fast-charging technology.
BMW i3 (0.01408) and Kia EV6 (0.01481) also perform efficiently.
Models like Audi Q4 e-tron and Hyundai Kona Electric deliver consistent but slightly slower performance.
Analyst Note:
Tesla’s charging systems remain industry benchmarks, combining fast-charging capability with effective range output.

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
This query evaluates performance cost-efficiency among vehicle categories.
Sedans provide the best value, with the lowest cost per max speed (7.95) and acceleration (239.78).
Trucks and Hatchbacks show higher cost values, reflecting greater power requirements for speed and torque.
Analyst Note:
Sedans strike the best cost-performance balance, making them ideal for users seeking efficiency and performance at lower operational costs.


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
This output examines how environmental temperature affects EV battery range in different regions.
North America records the highest average range (~392 km) in cold climates, likely due to better thermal management systems.
Europe maintains steady performance across climates (~377–378 km).
Asia and Australia show more variation, indicating greater sensitivity to heat and temperature fluctuations.
Analyst Note:
Temperature plays a critical role in EV performance; vehicles in temperate or well-cooled environments achieve greater driving range.
