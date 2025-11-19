 #  🚕 Yellow Cabs Project
---
## Scope of This Project
Yellow Cabs Taxi Co. is a mid-sized urban transportation company serving thousands of New Yorkers every day. Over the past year, the company has struggled with uneven ride demand, growing operating costs, and rising customer complaints about wait times and service quality. Although the company collects rich trip-level data (timestamps, distances, fares, tips, and locations), it has not been using analytics to guide operational decisions.

To address these challenges, the CEO hired me as a data analyst to uncover insights, optimize operations, and identify opportunities to improve efficiency, profitability, and customer satisfaction.

Throughout this project, I'll ultilize the P-A-C-E framework to solve this problem.
1. **PLAN**: Identify scope of the project
2. **ANALYZE**: Exploratory Data Analysis (EDA)
3. **CONSTRUCT**: Uncover relationship within data
4. **EXECUTE**: Collaborates with data team / stakeholers
---
## 1. **_PLAN_**: Identify scope of the project

- **Here is the list of five questions the business owner would like to know:**
1. What drives our ride demand throughout the day, week, and month?
2. What factors influence trip duration?
3. What pickup zones/borough generate the highest revenue and its average?
4. At what times of day are trips most profitable?
5. How does passenger tipping behavior vary by time, distance, and borough?


- **Project Proposal**
<img width="750" height="600" alt="image" src="https://github.com/user-attachments/assets/0567ea7c-fcc0-47e9-a409-e700e72a3cca" />
<img width="750" height="600" alt="image" src="https://github.com/user-attachments/assets/5aedaed6-0e53-44b7-ba99-d09ece956f85" />




  
---
## 2. _**ANALYZE**_: Exploratory Data Analysis (EDA)

### 2.1 Data Source
- NYC Yellow Taxi trip data (sampled from 2017 records).
- NYC PULocationID 
- Each record includes pickup & dropoff datetimes, passenger count, trip distance, pickup and dropoff location IDs, fare components, total amount, and tips.


### 2.2 Data Cleaning

Key cleaning steps:
- Converted `tpep_pickup_datetime` and `tpep_dropoff_datetime` from string to proper `datetime` objects.
- Created separate `pickup_date`, `pickup_time`, `dropoff_date`, and `dropoff_time` columns for easier time-based analysis.
- Removed outliers

### 2.3 Feature Engineering

To support the business questions, I engineered several new features:

- **Time-based features**
  - `pickup_hour` – hour of day (0–23).
  - `pickup_dow` – day of week (0 = Monday, 6 = Sunday).
  - `pickup_month` – calendar month.
  - `time_of_day` – categorical bucket (Morning, Afternoon, Evening, Night).

- **Trip metrics**
  - `trip_duration` – time delta between pickup and dropoff.
  - `trip_duration_minutes` – duration in minutes.
  - Distance bins (0–5, 6–10, 11–15, 16–20, 21–25, 26+ miles).

- **Revenue & tipping metrics**
  - `rev_per_min` – total fare per minute (profitability measure).
  - `tip_rate` – tip amount as a percentage of total fare.

- **Location ID**
  - Merged trip data with the NYC taxi zone lookup to map `PULocationID` to `Zone_x` and `Borough_x`.

### 2.4 EDA by Business Question

**Q1 – Ride demand by day, week, and month**

- Created bar charts of trip counts:
  - By hour of day → revealed clear morning and evening demand peaks.
  - By day of week → showed relatively even demand with slightly higher volume on certain weekdays.
  - By month → highlighted softer demand in the summer months compared to the rest of the year.
- Built a heatmap of `pickup_hour × pickup_dow` to visualize combined patterns and identify true demand “hotspots”.
---
**Q2 – Factors influencing trip duration**

- Calculated and visualized the distribution of `trip_duration_minutes` (with outliers removed).
- Binned trip duration into 0–9, 10–19, 20–29, 30–39, 40–49, 50–60 minutes to understand common trip lengths.
- Explored the relationship between `trip_distance` and `trip_duration_minutes`:
  - Computed correlation before and after removing extreme outliers.
  - Plotted a scatter plot to show how longer distances generally lead to longer trips, with variability due to traffic and routing.
- Compared average duration across:
  - Different hours of day.
  - Time-of-day categories (Morning vs Afternoon vs Evening vs Night).
  - Top pickup zones.
---
**Q3 – Revenue and demand by borough / zone**

- Aggregated total revenue (`total_amount`) and number of trips by `Borough_x`.
- Calculated:
  - **Total revenue by borough** (where the money is).
  - **Average revenue per trip by borough** (how valuable a typical ride is).
- Identified top pickup zones by volume and looked at their average trip duration and fare, highlighting zones like Midtown, Penn Station/Madison Sq West, and Times Sq/ Theatre District.
  
---
**Q4 – Times of day when trips are most profitable**

- Defined **profitability** as `rev_per_min` (revenue per minute of trip).
- Grouped by `pickup_hour` to compute average `rev_per_min`.
- Visualized a bar chart of revenue per minute by hour:
  - Showed that early-morning hours (around 3–6 AM) can be very profitable on a per-minute basis.
  - Discussed the tradeoff: these hours have high revenue per trip but low overall demand.

---
**Q5 – Tipping behavior across time, distance, and borough**

- Calculated `tip_rate` and limited it to 0–100% to remove erroneous records.
- Analyzed average tip rate:
  - By `pickup_hour` → Morning and evening trips showed the highest tip rates.
  - By distance bins → Mid-range trips had slightly higher tip percentages; very long trips often had lower tip rates.
  - By `Borough_x` → Brooklyn and Manhattan displayed higher tipping behavior than the Bronx.
- Created an hour × distance **heatmap of tip_rate** to identify combinations of time and distance that yield the most generous tips.
---
## 3. _**CONSTRUCT**_: Uncover relationship within data

In this phase I moved from raw EDA to more structured metrics and visual stories that directly answer the stakeholder questions.

### 3.1 Key Metrics

- **Demand metrics**
  - `trips_per_hour`, `trips_per_day_of_week`, `trips_per_month`.
  - Heatmap of trips by `pickup_hour × pickup_dow`.

- **Service & duration metrics**
  - Average trip duration by:
    - Hour of day.
    - Time-of-day category (Morning/Afternoon/Evening/Night).
    - Top pickup zones.

- **Revenue metrics**
  - Total and average revenue by borough and by top pickup zone.
  - `rev_per_min` by hour of day to capture true profitability patterns.

- **Tipping metrics**
  - Overall average `tip_rate`.
  - Tip rate by hour, distance bin, and borough.
  - Hour × distance tipping heatmap.

### 3.2 Patterns & Relationships

From these metrics and visualizations, several important relationships were constructed:

- **Time & Demand:** clear morning and evening peaks in ride volume, plus specific hour × day combinations where demand is strongest.
- **Distance & Duration:** longer trips take more time but with a lot of spread, likely due to traffic and route choices.
- **Location & Revenue:** Manhattan-based zones and certain high-traffic hubs generate disproportionate revenue and trips.
- **Time & Profitability:** early-morning trips provide high revenue per minute despite lower demand.
- **Time / Distance / Borough & Tipping:** tipping behavior depends jointly on when the trip occurs, how far it goes, and which borough it starts in.



---
## 4. _**EXECUTE**_: Collaborates with data team / stakeholers

In the final phase, I translated the analytical findings into a concise executive-level story and a set of actionable recommendations for Yellow Cabs leadership.

### 4.1 Executive Summary Presentation

I prepared a slide deck that:

- States the business context and project objective.
- Introduces the five guiding business questions.
- Shows key visualizations:
  - Demand by hour and by hour × day of week.
  - Trip duration distribution and typical trip length.
  - Revenue and demand by borough.
  - Revenue per minute by hour (profitability).
  - Tipping behavior by hour and the hour × distance tip-rate heatmap.
- Summarizes insights
- Recommendations

### 4.2 Key Recommendations (High Level)

- **Align driver schedules with demand peaks.**  
  Increase coverage during morning and evening rush hours, and ensure enough drivers in high-demand zones like Midtown and Penn Station.

- **Target profitable but under-served hours.**  
  Early-morning hours (around 3–6 AM) have high revenue per minute; targeted incentives could make these shifts attractive to drivers. Though, its down side was limited ride requested.

- **Focus on high-value boroughs and zones.**  
  Boroughs with strong revenue and tipping behavior (e.g., Manhattan and Brooklyn) should receive proportionally more driver coverage.

- **Monitor and educate around tipping patterns.**  
  Share insights on when and where tips are strongest so drivers can set realistic expectations and prioritize valuable trips.

