#  Employment & Business Density Dynamics Analysis in Sydney

![prodema-rustik-wood-ey-centre-fjmt-01](https://github.com/user-attachments/assets/19c15dd3-2df1-4de1-ae4d-729502dbbe99)

## Table of Contents
[Project Overview](#project-overview)

[Project goals and objectives](#project-goals-and-objectives)

[About the data](#about-the-data)

[Dataset Attributes Overview](#dataset-attributes-overview)

[Tools and libraries](#️-tools-and-libraries)

[Methodology](#methodology)

- [Data preprocessing](#data-preproccessing)

- [Data Visualization](#data-visualization)

[The key insights](#the-key-insights)

- [The Driving Factors Behind Business Growth](#the-driving-factors-behind-business-growth)
  
- [How Has Employment Shifted?](#How-Has-Employment-Shifted)

- [Sydney’s Vision for Industry-Focused Development](#Sydney’s-Vision-for-Industry-Focused-Development)

- [What’s Next for Business and Employment Trends?](#What’s-Next-for-Business-and-Employment-Trends?)

[Project challenges](#project-challenges)

[Conclusion](#conclusion)

## Project Overview
The Sydney Floor Space and Employment Survey is a comprehensive study conducted every five years to track trends in business activity, employment, and floor space utilization across the City of Sydney. The survey captures a snapshot of the city's built environment and economic structure, offering valuable insights into how businesses and jobs have evolved over time

## Project Goals and Objectives
🎯 Goals:
The primary goal of this project is to analyze and visualize the relationship between business growth, employment trends, and floor space utilization in Sydney from 2007 to 2022. By leveraging SQL for data processing and Power BI for visualization, this study aims to provide valuable insights into economic shifts, workforce density, and commercial development patterns. Additionally, it seeks to assess the impact of urban expansion and industry changes on employment rates and building usage, helping policymakers, researchers, and business owners make informed decisions. ✅

🔍 Key Objectives: 
- Preprocess and clean the dataset using SQL for accurate analysis. 🛠️
- Develop interactive visualizations in Power BI to explore trends across different years. 📈
- Analyze business growth patterns, job market shifts, and commercial space utilization. 🏢
- Identify correlations between floor space availability, employment rates, and business activity. 🔗
- Evaluate the impact of economic changes (e.g., post-pandemic recovery) on Sydney’s commercial landscape. 💼

## About the Data
This dataset provides a comprehensive snapshot of Sydney’s business landscape over four survey years: 2007, 2012, 2017, and 2022. Each dataset consists of 9 columns and captures insights into business establishments, employment figures, and floor space usage across different suburbs (Floor Space and Employment Survey - City of Sydney, 2024). The number of records varies per year, with 1,117 rows in 2022, 1,103 in 2017, 1,058 in 2012, and 1,057 in 2007.📊

This structured dataset allows for an in-depth analysis of business growth, employment density, and commercial development trends. By tracking changes over time, we can assess how industries have expanded or declined, identify shifts in workforce distribution, and explore the relationship between employment density and available commercial space. The dataset serves as a valuable foundation for understanding economic shifts, urban planning strategies, and the evolving structure of Sydney’s business ecosystem.🏙️

## Dataset Attributes Overview
- OBJECTID: A unique identifier for each record in the dataset.

- BLOCKNUM: The unique block number representing a specific geographical area.

- InternalFloorArea: The total internal floor space (measured in square meters) allocated for commercial and employment purposes.

- FullTimeEmployees: The total number of full-time employees working in the block.

- PartTimeEmployees: The total number of part-time employees working in the block.

- TotalEmployees: The combined count of full-time and part-time employees in the block.

- Businesses: The total number of businesses operating within the block.

- Shape__Area: The geographic area (in square meters) of the block, representing its physical size.

- Shape__Length: The perimeter length (in meters) of the block’s geographic boundary.

- Perimeter(2022): The perimeter size (in meters) of the block’s geographic boundary.

## Tools and Libraries
- MySQL: Data processing and cleaning.
- Power BI: Data visualization and reporting.

## Methodology

### Data Collecting

The datasets for this analysis were collected from the City of Sydney Council’s Open Data Portal (Sydney Data Hub), which provides publicly available records on land use, planning, employment, and commercial development.

This study utilizes four datasets from the Floor Space and Employment Survey (FES), covering the years 2007, 2012, 2017, and 2022. Each dataset contains employment figures, business counts, and internal floor space utilization for different suburbs across Sydney. The data is structured consistently across all four years, allowing for seamless comparison and trend analysis.

- 2007 Floor Space and Employment Survey – 1,057 records
- 2012 Floor Space and Employment Survey – 1,058 records
- 2017 Floor Space and Employment Survey – 1,103 records
- 2022 Floor Space and Employment Survey – 1,117 records

### Data Preprocessing 
```SQL
-- Input data checking 
Select * 
From  floor_space_22
Limit 20;
```
![Screenshot 2025-02-13 103936](https://github.com/user-attachments/assets/48d24610-9d67-4b7d-97c3-f0f2fec93abc)

Firstly, the dataset is checked to ensure that MySQL has installed the dataset properly.

```SQL
-- Checking null values
Select Count(*) as total_rows,
Sum(case when blocktxt is null then 1 else 0 end) as null_blocktxt,
Sum(case when internal_floorarea is null then 1 else 0 end) as null_internal_floor_area,
Sum(case when businesses is null then 1 else 0 end) as null_businesses,
Sum(case when total_jobs is null then 1 else 0 end) as null_employees,
Sum(case when perimeter is null then 1 else 0 end) as null_perimeter,
Sum(case when Shape__Area is null then 1 else 0 end) as null_Shape__Area,
Sum(case when Shape__Length is null then 1 else 0 end) as null_Shape__Length
From floor_space_22;
```
![Screenshot 2025-02-13 104257](https://github.com/user-attachments/assets/457b90df-dd6c-4faa-844f-27d07bb883bf)

This query checks for missing (NULL) values in the floor_space_22 dataset by counting how many NULLs exist in each column. The output shows a total of 2232 rows, with 0 NULL values in all columns, meaning the dataset has no missing data.

```SQL
-- Renaming "OBJECTID" column
Alter table floor_space_22
Rename column ï»¿OBJECTID to Num;

-- Droping "BLOCKTXT" column
Alter table floor_space_22
Drop column BLOCKTXT;
```

We first renamed the "OBJECTID" column to "Num" for better readability and dropped the "BLOCKTXT" column as it was not needed in our analysis. These changes help streamline the dataset and make it easier to work with.

```SQL
-- Checking duplicated values
Select *, count(*) as duplicated_count
From floor_space_22
Group by Num, BLOCKNUM, Total_FullTime_Jobs, Total_PartTime_Jobs, Total_Jobs, Businesses, Internal_FloorArea, PERIMETER, Shape__Area, Shape__Length
Having count(*) > 1;
```
![Screenshot 2025-02-13 110654](https://github.com/user-attachments/assets/5c52e00d-c1d3-4455-9d42-6fa64cbac411)

Next, we checked for duplicate values by grouping the data based on key attributes and counting occurrences. The output showed no duplicate records, this means that all rows in the dataset are unique

```SQL
-- Adding column
Set sql_safe_updates = 0;
Alter table floor_space_22 add column suburb varchar(50);

Update floor_space_22
Set suburb = 
    Case 
        When blocknum between 1000 and 1199 then 'Alexandria'
        When blocknum between 1200 and 1299 or blocknum between 1073 and 1078 then 'Zetland'
        When blocknum between 300 and 357 then 'Pott Point'
        When blocknum between 370 and 399 then 'Moore Park'
        When blocknum between 1 and 241 or blocknum between 1370 and 1376 then 'Sydney Cbd'
        When blocknum between 900 and 999 or blocknum between 839 and 845 then 'Glebe'
        When blocknum between 800 and 899 then 'Camperdown'
        When blocknum between 400 and 599 then 'Darlinghurst And Surry Hill'
        When blocknum between 600 and 799 then 'Redfern, Erskineville And Macdonaldtown'
        When blocknum between 1300 and 1410 then 'Redfern, Erskineville And Macdonaldtown'
        Else 'Unknown'
    End;
```
![Screenshot 2025-02-13 110953](https://github.com/user-attachments/assets/7d4c6540-97a9-4271-8f47-0a3dc977de1c)

We added a new column called "suburb" to the dataset and assigned suburb names based on the BLOCKNUM ranges. This categorization helps group data by location, making it easier to analyze growth patterns across different suburbs.

```SQL
-- Checking for "Unknown" values in suburb column
Select * 
From  floor_space_22
Where Businesses > 50
Limit 100;

Select blocknum, suburb 
From floor_space_22
Where suburb = "Unknown"
Order By blocknum;
```
Then we checked for any "Unknown" values in the suburb column by filtering blocks with more than 50 businesses and reviewing unassigned block numbers.

```SQL
Update floor_space_22
Set suburb = 
    Case  
        When blocknum between 1000 and 1199 then 'Alexandria'
        When blocknum between 1200 and 1299 or blocknum between 1073 and 1078 then 'Zetland'
        When blocknum between 0300 and 0357 then 'Pott Point'
        When blocknum between 0370 and 0399 or blocknum between 0360 and 0363 then 'Moore Park'
        When blocknum between 0001 and 0241 or blocknum between 1370 and 1376 then 'Sydney Cbd'
        When blocknum between 0900 and 0999 or blocknum between 0839 and 0845 then 'Glebe'
        When blocknum between 0800 and 0899 then 'Camperdown'
        When blocknum between 0400 and 0599 then 'Darlinghurst And Surry Hill'
        When blocknum between 0600 and 0799 then 'Redfern, Erskineville And Macdonaldtown'
        When blocknum between 1300 and 1410 then 'Redfern, Erskineville And Macdonaldtown'
        Else 'Unknown'  
    End;
```
After that, we updated the column again. 

### Exploratory Data Analysis (EDA)
#### 2022 Dataset
```SQL
-- Statistical analysis
-- Min, max, avg floor area
SELECT 'Min Internal Floor Area' AS attribute, MIN(internal_floorarea) AS value FROM floor_space_22
UNION ALL
SELECT 'Max Internal Floor Area', MAX(internal_floorarea) FROM floor_space_22
UNION ALL
SELECT 'Avg Internal Floor Area', AVG(internal_floorarea) FROM floor_space_22
UNION ALL
SELECT 'Internal Floor Area Standard Deviation' AS attribute, stddev_samp(internal_floorarea) AS value FROM floor_space_22
UNION ALL
SELECT 'Min Block', MIN(blocknum) FROM floor_space_22
UNION ALL
SELECT 'Max Block', MAX(blocknum) FROM floor_space_22
UNION ALL
SELECT 'Avg Block', AVG(blocknum) FROM floor_space_22
UNION ALL
SELECT 'Block Standard Deviation' AS attribute, stddev_samp(blocknum) AS value FROM floor_space_22
UNION ALL
SELECT 'Min Part-Time Jobs', MIN(total_parttime_jobs) FROM floor_space_22
UNION ALL
SELECT 'Max Part-Time Jobs', MAX(total_parttime_jobs) FROM floor_space_22
UNION ALL
SELECT 'Avg Part-Time Jobs', AVG(total_parttime_jobs) FROM floor_space_22
UNION ALL
SELECT 'Part-Time Jobs Standard Deviation' AS attribute, stddev_samp(Total_PartTime_Jobs) AS value FROM floor_space_22
UNION ALL
SELECT 'Min Full-Time Jobs', MIN(total_fulltime_jobs) FROM floor_space_22
UNION ALL
SELECT 'Max Full-Time Jobs', MAX(total_fulltime_jobs) FROM floor_space_22
UNION ALL
SELECT 'Avg Full-Time Jobs', AVG(total_fulltime_jobs) FROM floor_space_22
UNION ALL
SELECT 'Full-Time Jobs Standard Deviation' AS attribute, stddev_samp(total_fulltime_jobs) AS value FROM floor_space_22
UNION ALL
SELECT 'Min Total Jobs', MIN(total_jobs) FROM floor_space_22
UNION ALL
SELECT 'Max Total Jobs', MAX(total_jobs) FROM floor_space_22
UNION ALL
SELECT 'Avg Total Jobs', AVG(total_jobs) FROM floor_space_22
UNION ALL
SELECT 'Total Jobs Standard Deviation' AS attribute, stddev_samp(total_jobs) AS value FROM floor_space_22
UNION ALL
SELECT 'Min Businesses', MIN(businesses) FROM floor_space_22
UNION ALL
SELECT 'Max Businesses', MAX(businesses) FROM floor_space_22
UNION ALL
SELECT 'Avg Businesses', AVG(businesses) FROM floor_space_22
UNION ALL
SELECT 'Businesses Standard Deviation' AS attribute, stddev_samp(Businesses) AS value FROM floor_space_22
UNION ALL
SELECT 'Min Perimeter', MIN(perimeter) FROM floor_space_22
UNION ALL
SELECT 'Max Perimeter', MAX(perimeter) FROM floor_space_22
UNION ALL
SELECT 'Avg Perimeter', AVG(perimeter) FROM floor_space_22
UNION ALL
SELECT 'Perimeter Standard Deviation' AS attribute, stddev_samp(PERIMETER) AS value FROM floor_space_22;
```
![Screenshot 2025-03-18 120126](https://github.com/user-attachments/assets/52739cd4-1aa3-471f-9efd-43af9f78e14d)

This query calculates key statistical measures (minimum, maximum, average, and standard deviation) for multiple attributes in the dataset, such as internal floor area, block numbers, jobs, businesses, and perimeter. It uses UNION ALL to combine multiple SELECT statements into a single result.

- Internal Floor Area ranges from 0 to 438,193.105, with an average of 34,066.31 and a high standard deviation (46,235.66), indicating large variations.
- Block Numbers range from 1 to 1,408, with an average of 641.55 and moderate variation (373.42).
- Part-Time Jobs and Full-Time Jobs show significant variation, with max values of 2,562 and 18,068, respectively. The standard deviation is particularly high for Full-Time Jobs (1,174.78).
- Total Jobs range from 0 to 20,284, averaging 465.80, with a standard deviation of 1,353.74, showing high dispersion.
- Businesses have an average of 19.37, but the standard deviation (40.96) suggests some areas have significantly more businesses.
Perimeter values range from 117.75 to 5,606.76, showing large variations in plot sizes.

```SQL
Select
	internal_floorarea,
    PERIMETER,
	total_jobs,
    businesses,
    round(total_jobs/internal_floorarea, 2) as jobs_per_sqm
From floor_space_22
Where internal_floorarea > 0
Order by jobs_per_sqm desc
Limit 20;
```

```SQL
Alter table floor_space_22 ADD COLUMN jobs_sqm float;

Update floor_space_22
	Set jobs_sqm = CASE 
    When internal_floorarea = 0 OR internal_floorarea Is null or total_jobs = 0 
    Then 0  -- No jobs or no business, so set to 0
    Else Round(total_jobs / NULLIF(internal_floorarea, 0), 2) 
End;
```
![Screenshot 2025-02-15 094626](https://github.com/user-attachments/assets/5a999ade-0c62-4aae-9912-df789c139c99)

We calculated jobs per square meter (jobs_per_sqm) by dividing total jobs by internal floor area, filtering out entries with zero floor area. The top 20 records with the highest job density were retrieved. Then, we added a new column jobs_sqm to store this value and updated it using a conditional formula to handle cases where floor area or jobs were zero.

> The output displays the top 20 records with the highest jobs per square meter (jobs_per_sqm). The highest value is 0.17 jobs/sqm, meaning this space was leased to one business that has the highest job density despite its small floor area. Larger spaces tend to have lower job densities, with most values clustering around 0.04 to 0.09 jobs/sqm. The number of businesses varies significantly, indicating that job density is not solely dependent on business count but also on floor area utilization.

```SQL
-- Internal floor area vs jobs correlation
Select 
    Round(
        (Sum(internal_floorarea * total_jobs) - (Sum(internal_floorarea) * Sum(total_jobs)) / Count(*)) /
        (Sqrt(
            (Sum(internal_floorarea * internal_floorarea) - (Sum(internal_floorarea) * Sum(internal_floorarea)) / Count(*)) * 
            (Sum(total_jobs * total_jobs) - (Sum(total_jobs) * Sum(total_jobs)) / Count(*))
        ))
    , 2) as floor_jobs_corr,

    Round(
        (Sum(internal_floorarea * businesses) - (Sum(internal_floorarea) * Sum(businesses)) / Count(*)) /
        (Sqrt(
            (Sum(internal_floorarea * internal_floorarea) - (Sum(internal_floorarea) * Sum(internal_floorarea)) / Count(*)) * 
            (Sum(businesses * businesses) - (Sum(businesses) * Sum(businesses)) / Count(*))
        ))
    , 2) as floor_businesses_corr;
```
![Screenshot 2025-02-15 094648](https://github.com/user-attachments/assets/581b5b4b-21e7-4301-901e-bbac445f2fa4)

Next, we calculated the Pearson correlation coefficients between internal floor area and total jobs (floor_jobs_corr), as well as internal floor area and businesses (floor_businesses_corr). 

> The results show a strong positive correlation (0.78) between floor area and total jobs, indicating that larger spaces tend to accommodate more jobs. Similarly, there is a moderate positive correlation (0.67) between floor area and businesses, suggesting that larger spaces generally house more businesses, but the relationship is not as strong as with jobs.

```SQL
-- Suburb vs Jobs & businesses

From floor_space_22;

Select
	suburb,
	Sum(total_jobs),
    Sum(businesses),
	Round(avg(internal_floorarea), 2) as avg_floorarea
From floor_space_22
Group by suburb
Order by sum(total_jobs) desc;
```
![Screenshot 2025-02-15 101309](https://github.com/user-attachments/assets/14c784a9-d647-4f22-a603-6d05c00e7cc1)

We summarized suburbs by total jobs, businesses, and average floor area. Then we groups data by suburb, calculates totals and averages, and orders the results by the highest number of jobs.
- Sydney CBD: Dominates with 358,683 total jobs, 12,242 businesses, and an average floor area of 70,378.34 m².

- Darlinghurst: Trails behind with 64,882 jobs, 3,351 businesses, and an average floor area of 21,845.34 m².

- Redfern, Erskineville: 38,911 jobs, 1,733 businesses, and 19,068.69 m² average floor area.

- Alexandria: Notable for 27,725 jobs, 1,639 businesses, but a large 32,636.19 m² average floor area, indicating industrial use.

- Zetland: 9,881 jobs, 884 businesses, with 16,399.42 m² average floor area.

- Moore Park: 7,345 jobs, 632 businesses, and 13,622.53 m² average floor area.

> The output shows Sydney CBD as the dominant hub with the highest jobs, businesses, and a large floor area. Suburbs like Darlinghurst and Redfern trail with fewer jobs and businesses but maintain decent average floor areas. Alexandria stands out with spacious areas despite lower job numbers, suggesting possible industrial use.

```SQL
-- Full vs part time jobs

Select 
	blocknum, suburb, total_jobs, Total_fulltime_jobs, Total_parttime_jobs
From floor_space_22
Where Total_fulltime_jobs > Total_parttime_jobs
order by Total_fulltime_jobs desc
Limit 100;
```
We wanted to find out and compare the suburbs' building full-time and part-time job employments by retrieving data for blocks in suburbs where the number of full-time jobs exceeds part-time jobs for the first query and vice verca for the second query. We selected block number, suburb, total jobs, full-time jobs, and part-time jobs, ordering the results by full-time jobs in descending order for first query and part-time jobs in the same order in the second query, then limiting the output to the top 100 rows.

![Screenshot 2025-02-15 161207](https://github.com/user-attachments/assets/255ee0df-9784-4f71-b694-e05bf5e7f697)

- Redfern, Erskineville leads with 18,068 full-time jobs and only 2,216 part-time jobs, suggesting a predominance of full-time employment in that area.

- Several entries for Sydney CBD are listed, with full-time jobs ranging from 9,933 to 5,155, significantly outnumbering part-time jobs.

- Camperdown also stands out with 10,289 full-time jobs and just 633 part-time jobs.

> This reflects a trend where central and employment-heavy suburbs like Sydney CBD dominate in full-time job availability

```SQL
Select 
	blocknum, suburb, total_jobs, Total_parttime_jobs, Total_fulltime_jobs
From floor_space_22
Where Total_parttime_jobs > Total_fulltime_jobs  
order by Total_parttime_jobs desc
Limit 100;
```
![Screenshot 2025-02-15 161225](https://github.com/user-attachments/assets/a7b587d2-9205-48f3-9be0-394ca717dd13)

- Glebe: Leads with 1,258 part-time jobs and only 471 full-time jobs, showing a significant preference for part-time employment.

- Sydney CBD: Multiple blocks have more part-time jobs, including one with 1,086 part-time jobs versus 250 full-time jobs and another with 805 part-time jobs compared to 377 full-time jobs. This suggests a mix of part-time opportunities within the central business area.

- Moore Park: Notable with 772 part-time jobs and 597 full-time jobs, reflecting a balance but leaning toward part-time work.

- Darlinghurst: One block has 299 part-time jobs to just 125 full-time jobs, showing a clear trend toward part-time employment in that area.

- Alexandria: Contains a block with 218 part-time jobs compared to 157 full-time jobs.

> This output highlights suburbs where part-time jobs dominate, potentially indicating industries like retail or hospitality that favor flexible roles.

#### 2007-2022 Datasets

```SQL
Select
    floor_space_22.blocknum, 
    floor_space_07.PartTimeEmployees AS parttime_2007,
    floor_space_12.PartTimeEmployees AS parttime_2012,
    floor_space_17.PartTimeEmployees AS parttime_2017,
    floor_space_22.Total_PartTime_Jobs AS parttime_2022, 
    Concat(Round(Coalesce((floor_space_22.Total_PartTime_Jobs - floor_space_07.PartTimeEmployees) / NULLIF(floor_space_07.PartTimeEmployees, 1) * 100, 0), 1), '%') AS parttimejob_growth_2007_2022_pct,
    floor_space_22.suburb
From floor_space_22
Inner join floor_space_17 ON floor_space_22.blocknum = floor_space_17.blocknum
Inner join floor_space_12 ON floor_space_22.blocknum = floor_space_12.blocknum
Inner join floor_space_07 ON floor_space_22.blocknum = floor_space_07.blocknum
Order by blocknum, suburb;
```
![Screenshot 2025-02-20 132627](https://github.com/user-attachments/assets/b721f5af-518d-40aa-b9ac-425a18f2740f)

We used these queries to track the growth of part-time and full-time jobs across different blocks from 2007 to 2022. Datasets were joined from multiple years (2007, 2012, 2017, and 2022) based on blocknum and retrieve the number of employees for each year. The parttimejob_growth_2007_2022_pct and fulltimejob_growth_2007_2022_pct fields calculate the percentage growth in jobs over this period, ensuring that division by zero is avoided using NULLIF. The results are then sorted by block and suburb, providing a clear view of employment trends over time.

```SQL
Select 
    floor_space_22.blocknum, 
    floor_space_07.FullTimeEmployees AS fulltime_2007,
    floor_space_12.FullTimeEmployees AS fulltime_2012,
    floor_space_17.FullTimeEmployees AS fulltime_2017,
    floor_space_22.Total_FullTime_Jobs AS fulltime_2022,
    Concat(Round(Coalesce((floor_space_22.Total_FullTime_Jobs - floor_space_07.FullTimeEmployees) / NULLIF(floor_space_07.FullTimeEmployees, 1) * 100, 0), 1), '%') AS fulltimejob_growth_2007_2022_pct,
    floor_space_22.suburb
From floor_space_22
Inner join floor_space_17 ON floor_space_22.blocknum = floor_space_17.blocknum
Inner join floor_space_12 ON floor_space_22.blocknum = floor_space_12.blocknum
Inner join floor_space_07 ON floor_space_22.blocknum = floor_space_07.blocknum
Order by blocknum, suburb;
```
![Screenshot 2025-02-20 132605](https://github.com/user-attachments/assets/57faec11-8287-4697-a952-51b3ab45e0a6)



```SQL
Select 
    floor_space_22.blocknum, 
    floor_space_07.TotalEmployees AS total_jobs_2007,
    floor_space_12.TotalEmployees AS total_jobs_2012,
    floor_space_17.TotalEmployees AS total_jobs_2017,
    floor_space_22.total_jobs as total_jobs_2022,
    Concat(Round(Coalesce((floor_space_22.Total_Jobs - floor_space_07.TotalEmployees) / NULLIF(floor_space_07.TotalEmployees, 1) * 100, 0), 1), '%') AS Totaljob_growth_2007_2022_pct,
    floor_space_22.suburb
From floor_space_22
Inner join floor_space_17 ON floor_space_22.blocknum = floor_space_17.blocknum
Inner join floor_space_12 ON floor_space_22.blocknum = floor_space_12.blocknum
Inner join floor_space_07 ON floor_space_22.blocknum = floor_space_07.blocknum
Order by blocknum, suburb;
```
![Screenshot 2025-02-20 132254](https://github.com/user-attachments/assets/83931c6a-ac69-48c6-b176-9b637f1f10dd)

These queries analyze the growth and trends of three key attributes—total jobs, internal floor space, and businesses—from 2007 to 2022. By joining data from different years based on blocknum, this can help retrieve historical values and calculate percentage growth over time. The Totaljob_growth_2007_2022_pct, floorspace_growth_2007_2022_pct, and business growth percentages help in understanding how employment, physical infrastructure, and business activity have evolved. The use of Coalesce and NULLIF ensures robust calculations by handling potential missing or zero values. By visualizing these attributes, we can witness growth patterns or fluctuating trends and explore potential correlations among them


```SQL
Select
    floor_space_22.blocknum, 
    floor_space_07.InternalFloorArea AS total_space_2007,
    floor_space_12.InternalFloorArea AS total_space_2012,
    floor_space_17.InternalFloorArea AS total_space_2017,
    floor_space_22.Internal_FloorArea AS total_space_2022,
    Concat(Round(Coalesce((floor_space_22.Internal_FloorArea - floor_space_07.InternalFloorArea) / NULLIF(floor_space_07.InternalFloorArea, 1) * 100, 0), 1), '%') AS floorspace_growth_2007_2022_pct,
    floor_space_22.suburb
From floor_space_22
Inner join floor_space_17 ON floor_space_22.blocknum = floor_space_17.blocknum
Inner join floor_space_12 ON floor_space_22.blocknum = floor_space_12.blocknum
Inner join floor_space_07 ON floor_space_22.blocknum = floor_space_07.blocknum
Order by blocknum, suburb;
```
![Screenshot 2025-02-20 132543](https://github.com/user-attachments/assets/1ac010d6-9d70-4def-a4bf-6df8b8f9a986)



```SQL
Select
    floor_space_22.blocknum, 
    floor_space_07.Businesses AS total_businesses_2007,
    floor_space_12.Businesses AS total_businesses_2012,
    floor_space_17.Businesses AS total_businesses_2017,
    floor_space_22.Businesses AS total_businesses_2022,
    Concat(Round(Coalesce((floor_space_22.Businesses - floor_space_07.Businesses) / NULLIF(floor_space_07.Businesses, 1) * 100, 0), 1), '%') AS floorspace_growth_2007_2022_pct,
    floor_space_22.suburb
From floor_space_22
Inner join floor_space_17 ON floor_space_22.blocknum = floor_space_17.blocknum
Inner join floor_space_12 ON floor_space_22.blocknum = floor_space_12.blocknum
Inner join floor_space_07 ON floor_space_22.blocknum = floor_space_07.blocknum
Order by blocknum, suburb;
```
![Screenshot 2025-02-20 132903](https://github.com/user-attachments/assets/4ca89f64-b7c1-49ef-a4c4-11091b713f62)



```SQL
Select 
    fs22.blocknum,
    Coalesce(fs07.totalemployees, 0) as jobs_2007,
    Coalesce(fs12.totalemployees, 0) as jobs_2012, 
    Coalesce(fs17.totalemployees, 0) as jobs_2017, 
    fs22.total_jobs as jobs_2022,
    fs22.suburb,
    
    Case 
        When Coalesce(fs07.totalemployees, 0) = 0 
             Then Concat((fs22.total_jobs - 0), '%') -- Growth relative to new jobs
        Else Concat(Round(((fs22.total_jobs - fs07.totalemployees) / fs07.totalemployees) * 100, 1), '%')
    End as totaljob_growth_2007_2022,
    Case
        When (fs22.total_jobs > fs17.totalemployees and fs17.totalemployees > fs12.totalemployees and fs12.totalemployees > fs07.totalemployees)
        Then 'Consistent Growth'
        Else 'Fluctuating'
    End as growth_pattern
From floor_space_22 fs22
Left join floor_space_17 fs17 on fs22.blocknum = fs17.blocknum
Left join floor_space_12 fs12 on fs22.blocknum = fs12.blocknum
Left join floor_space_07 fs07 on fs22.blocknum = fs07.blocknum
Order by jobs_2022 desc;
```

![Screenshot 2025-03-14 152254](https://github.com/user-attachments/assets/c9b4d728-79bc-4f15-bd3e-746f1350ae13)

We wanted to find out the job growth trends from 2007 to 2022 across different blocks, handling missing values with Coalesce. In order to do this, we calculate percentage job growth since 2007 and categorizes each block's trend as either "Consistent Growth" (if jobs increased steadily across all periods) or "Fluctuating" (if there were ups and downs). The results help in identifying areas with stable employment expansion versus those experiencing irregular job trends, which is valuable for workforce planning and economic analysis.

These insights are crucial for urban planning, economic development, and policy-making, allowing stakeholders to identify key areas of expansion or decline.

### Data Visualization 


![Screenshot 2025-03-18 165151](https://github.com/user-attachments/assets/48cbba7c-6a76-4938-b827-91350e14db27)


The bar chart (left) breaks down the number of full-time (purple) and part-time (cyan) jobs by suburb. Sydney CBD dominates with the highest employment, particularly in full-time jobs (261,382), followed by Redfern, Darlinghurst, and Alexandria. Other suburbs have significantly lower job counts, with part-time jobs generally being much lower than full-time jobs across most areas.

The pie chart (right) visualizes the total job distribution by suburb, showing that Sydney CBD accounts for nearly 70% of all jobs. Other suburbs contribute much smaller shares, with the next largest, Redfern, holding just around 9%. This highlights Sydney CBD as the primary employment hub, while other suburbs play relatively smaller roles in job distribution


![Screenshot 2025-03-12 125258](https://github.com/user-attachments/assets/3f481ead-c219-4e17-90dc-68de9a60b07e)


The first chart, which tracks the number of businesses across six key areas—Sydney CBD, Redfern, Erskineville and Macdonaldtown, Glebe, Darlinghurst and Surry Hills, and Alexandria—highlights a significant transformation in the commercial landscape between 2007 and 2022. Sydney CBD remains the dominant hub, reaching a peak of 13,830 businesses in 2017 before slightly declining to 12,165 in 2022. 
> This leadership position is largely due to its status as the financial, accounting, hospitality, and entertainment center of New South Wales, attracting a diverse range of businesses that benefit from the area's strong economic infrastructure and global connectivity. Meanwhile, suburbs such as Alexandria, Darlinghurst, and Surry Hills have experienced steady business growth, reflecting broader urban development trends. These shifts are driven by infrastructure improvements, enhanced transport connectivity, and strategic investments that encourage economic activity beyond the CBD (City Of Sydney Council, 2025).

The second chart, which examines job growth across multiple suburbs—including Alexandria, Camperdown, Darlinghurst and Surry Hills, Glebe, Moore Park, Potts Point, Redfern, Erskineville and Macdonaldtown, and Zetland—further reinforces this trend. Alexandria, in particular, has shown significant employment growth, with job numbers rising from 30,992 in 2007 to 42,367 in 2022. Similarly, Camperdown exhibited strong job expansion, reaching approximately 30,527 jobs in 2017 before experiencing a slight decline. 
> These employment trends highlight the impact of strategic investments in suburban infrastructure and deliberate policies aimed at decentralizing economic activity from Sydney CBD. The combination of private-sector expansion and government-led urban planning initiatives has positioned these suburbs as emerging economic hubs, complementing Sydney’s central business district while fostering broader regional growth with 1 to 2 industries center focusing on each suburbs (City Of Sydney Council, 2025).


![Screenshot 2025-03-12 125325](https://github.com/user-attachments/assets/97c7a8b8-7a09-4110-82f0-e3f3e1aefe19)


The left chart presents the total job growth in Sydney CBD from 2007 to 2022, showing a significant increase from 282,000 jobs in 2007 to 316,000 in 2012. The peak was reached in 2017 with 355,000 jobs before slightly declining to 354,000 in 2022, reflecting the impact of economic shifts, including the COVID-19 pandemic. 
> This trend aligns with Sydney’s strategic focus on maintaining its role as a commercial hub through infrastructure investments, business incentives, and improved transport networks (City Of Sydney Council, 2025). The growth indicates the success of policies that support office space expansion, business innovation, and industry diversification​.

The right treemap visualizes employment density by identifying the top 20 buildings with the highest jobs per square meter in 2022. The highest employment densities are found in commercial buildings within Sydney CBD, reinforcing the city’s strategy of optimizing urban space for business growth. 
> This aligns with Sydney’s long-term economic plan, which encourages vertical workspaces, mixed-use developments, and sustainable office designs to maximize land use efficiency and workforce concentration​.


![Screenshot 2025-03-15 113710](https://github.com/user-attachments/assets/bf292e7f-9e2e-4ac3-8b75-0774582b8c5f)


On the left chart, suburbs like Sydney CBD, Glebe, and Darlinghurst & Surry Hills have significantly more part-time jobs than full-time jobs. This trend could be linked to industries such as hospitality, retail, and tourism, which rely heavily on part-time or casual workers. The City of Sydney’s Economic Development Strategy emphasizes diversifying job opportunities and supporting flexible work arrangements, which may explain the higher part-time employment in these areas​ (City Of Sydney Council, 2025).

On the right chart, suburbs like Sydney CBD, Redfern, and Camperdown show a significantly higher number of full-time jobs, indicating the presence of corporate offices, education institutions, and healthcare facilities, which typically employ more full-time professionals. The strategy for 2025-2035 focuses on enhancing business investment and commercial space availability, which may continue to drive full-time job growth in these areas (City Of Sydney Council, 2025).


![Screenshot 2025-03-12 125619](https://github.com/user-attachments/assets/2d6b9bde-df1b-46dd-816b-439c42564cb6)


The first chart illustrates the internal floor space trends from 2007 to 2022 across various Sydney suburbs. Sydney CBD consistently holds the largest share of total floor space, increasing from 16.08M sqm in 2007 to 17.01M sqm in 2017, before slightly decreasing to 16.25M sqm in 2022. Other key suburbs like Redfern, Camperdown, and Alexandria have seen steady growth in floor space, suggesting ongoing urban development and commercial expansion. The slight decline in Sydney CBD’s floor space in 2022 may indicate post-pandemic office space adjustments, increased remote work trends, or redevelopment efforts shifting space allocation priorities.


![Screenshot 2025-03-12 125726](https://github.com/user-attachments/assets/156e2e69-2815-41ae-82cf-0e744f7269f0)


The second chart highlights the correlation between job growth, business expansion, and floor space trends from 2007 to 2022. While total jobs increased from 0.38M to 0.48M, and businesses showed steady growth, floor space expanded until 2017 but slightly declined by 2022. This shift reflects Sydney’s push for higher employment density, flexible workspaces, and mixed-use developments​
The rise of hybrid work models and office space efficiency has allowed job growth to continue without requiring proportional increases in physical space (City Of Sydney Council, 2025).


![Screenshot 2025-03-15 131734](https://github.com/user-attachments/assets/1c8550b7-d152-41f7-879c-3f1709bac6f6)


The map visualizes employment growth across Sydney suburbs, with larger bubbles representing areas with significant job growth. Key commercial hubs like Sydney CBD, Redfern, and Surry Hills exhibit strong employment expansion, aligning with the city's business districts and redevelopment projects.

The bar chart (right) quantifies the proportion of fluctuating vs. consistent growth across suburbs. It reveals that the majority of blocks (over 90% in most suburbs) experienced fluctuating growth, with only a small fraction showing consistent growth (represented in purple). Sydney CBD (8.94%) and Alexandria (8.16%) have slightly higher rates of consistent growth, suggesting stable economic activity in certain commercial zones.

> This shows that most buildings in Sydney experience fluctuating employment trends, likely due to business turnover, shifting industries, and evolving workspace demands. The few areas with consistent growth may reflect long-term commercial investments, stable corporate headquarters, or infrastructure-driven expansion.


![Screenshot 2025-03-12 125756](https://github.com/user-attachments/assets/af5bd3f6-78aa-45e0-b91b-f9b19146a9c6)


These heatmaps visualize the growth of total businesses (top) and total jobs (bottom) from 2007 to 2022 across Sydney suburbs. Sydney CBD dominates in both categories, showing the highest number of businesses (50,292 total) and jobs (1.3 million total), reinforcing its role as the city's economic hub. Other suburbs like Darlinghurst, Alexandria, and Redfern also show steady increases, indicating expanding commercial activity. While most suburbs experienced growth, some fluctuations are visible, reflecting changing business landscapes and employment trends. The consistent rise in both businesses and jobs highlights Sydney’s economic expansion and the increasing demand for commercial spaces and workforce opportunities.


![mapvis](https://github.com/user-attachments/assets/3ca77f99-70cd-4e8b-93c0-75fedaee1e2f)

The data highlights a growing diversity in business sectors across Sydney suburbs from 2007 to 2022, reflecting the city’s economic transformation and strategic development goals. The number of businesses increased from 19,571 in 2007 to 23,511 in 2017, before slightly declining to 21,618 in 2022. Over the years, Professional & Business Services saw a rise from 2,832 businesses in 2007 to 4,042 in 2017, reinforcing Sydney’s role as a corporate and financial hub. Retail & Personal Services also grew from 3,109 to 3,399 during the same period, while Food & Drink establishments expanded from 2,750 to 3,746, showing a steady demand for consumer-oriented industries.

This shift aligns with the City of Sydney’s Economic Development Strategy, which focuses on high-value industries, entrepreneurship, and business resilience​ (City Of Sydney Council, 2025).The Finance sector remained stable around 1,678–2,138 businesses, while Tourism-related businesses increased from 1,154 to 1,430 by 2017, reflecting Sydney’s push for cultural and tourism growth. The ICT sector consistently held over 1,000 businesses, supporting the city’s digital economy and tech innovation goals. These figures indicate that Sydney’s economy is adapting to changing workforce demands and urban development initiatives, ensuring long-term economic sustainability.

## Key Insights

### The Driving Factors Behind Business Growth
Sydney CBD remains the dominant business hub, with the highest number of businesses (50,292) and total jobs (1.3M) in 2022.
Professional & Business Services, Retail, and Food & Drink sectors have seen steady expansion, reinforcing Sydney’s role as a commercial and hospitality center.
The growth of ICT (1,047 businesses in 2017) and Finance (2,138 businesses in 2017) highlights the city’s transition toward a digital economy and financial services hub.

### How Has Employment Shifted?
Total jobs grew from 375K in 2007 to 478K in 2022, but some fluctuations occurred post-pandemic.
Full-time jobs consistently outnumber part-time roles, but certain suburbs, such as Darlinghurst and Redfern, have seen higher part-time employment trends, likely due to hospitality and service industries.
Employment density is highest in high-rise commercial zones, with Sydney CBD leading job concentration, aligning with Sydney’s urban planning strategy for maximizing land use efficiency.

### Sydney’s Vision for Industry-Focused Development
The City of Sydney’s Economic Development Strategy 2025-2035 outlines plans to decentralize employment hubs and transform specific suburbs into industry-focused centers:

- Camperdown is set to become a leading biomedical and life sciences hub, capitalizing on its proximity to major research institutions and hospitals (Grove, 2024).
- Eveleigh-Redfern is evolving into a technology and innovation precinct, attracting startups, digital enterprises, and advanced manufacturing (Hill, 2022).
Green Square and Zetland are planned as emerging creative and retail districts, supporting small businesses and local entrepreneurs.
- Ultimo and Pyrmont will continue to be centers for education, creative industries, and digital media, benefiting from existing universities and innovation clusters (City Of Sydney Council, 2022).
  
### What’s Next for Business and Employment Trends?
The city’s focus on innovation, tourism, and finance will likely continue to shape future employment trends.
Job decentralization could occur, with more businesses spreading beyond the CBD as transportation and infrastructure projects improve connectivity.
Sustainable development initiatives, such as mixed-use spaces and vertical workspaces, will play a role in reshaping business growth patterns.
Recommendations for Urban Planners and Business Owners
For policymakers: 
> Invest in suburban business districts to reduce congestion and promote local employment opportunities while aligning with Sydney’s economic strategy.
For business owners:
> Prioritize sectors aligned with Sydney’s growth plan, such as biomedical, technology, creative industries, and finance, to maximize long-term sustainability.
For researchers:
> Explore the correlation between building density, business growth, and employment trends to predict future urban expansion.

## Project Challenges
- Limited Geographic Scope 
The dataset primarily focuses on Sydney CBD and its adjacent suburbs (e.g., Camperdown, Redfern, Alexandria). However, it does not account for other major business districts like Parramatta, North Sydney, and Chatswood, which have experienced rapid growth in employment and commercial activities over the past decade. This limits the ability to compare Sydney’s broader metropolitan employment trends.
- Lack of Industry-Specific Data
While the dataset provides insights into total businesses and employment figures, it does not detail the industry breakdown per block. This makes it challenging to analyze how specific economic sectors (e.g., tech startups, finance, manufacturing) are shaping urban development within different suburbs.

The dataset does not include details on remote work trends, employee movement between jobs, or how workers commute—all of which have become important since the pandemic. Without this information, it's harder to see how flexible work policies and changing job preferences impact where businesses and employees are located.

## Conclusion
This project analyzed business and employment growth trends across key Sydney suburbs from 2007 to 2022. The data revealed steady growth in total jobs and businesses, with Sydney CBD remaining the dominant employment hub, while suburbs like Camperdown and Redfern are emerging as industry-specific centers. However, job growth patterns were largely fluctuating, indicating economic shifts and evolving business demands.

For urban planning based on Sydney Council strategies, the findings highlight the need for balanced job distribution, improved public transport access, and strategic infrastructure investments to support growing business districts. The Sydney Council’s plan to develop Camperdown as a biomedical hub and Eveleigh-Redfern as a tech innovation center aligns with these trends. Future strategies should focus on enhancing business diversity, supporting remote work-friendly spaces, and expanding data coverage to include other key metropolitan areas like Parramatta and North Sydney.

## References
Floor space and employment survey - City of Sydney. (2024, November 26). City of Sydney. https://www.cityofsydney.nsw.gov.au/research-reports/floor-space-employment-survey-2022.
City Of Sydney Council. (2022, April 6). More space for creativity and culture in Sydney | City of Sydney - News. News.cityofsydney.nsw.gov.au. https://news.cityofsydney.nsw.gov.au/articles/more-space-for-creativity-and-culture-in-sydney.
City Of Sydney Council. (2025, January 22). Economic development strategy. 
City of Sydney. https://www.cityofsydney.nsw.gov.au/strategies-action-plans/economic-development-strategy.
Grove, N. (2024, November 27). ISPT and ART acquire site to develop $585m Healthcare and Life Sciences Hub in Sydney’s premier healthcare precinct - ISPT. ISPT. https://ispt.com.au/news/healthcare/ispt-and-art-acquire-site-to-develop-585m-healthcare-and-life-sciences-hub-in-sydneys-premier-healthcare-precinct/
Hill, L. (2022, October 12). South Eveleigh: Sydney’s Newest Hub of Innovation. Work Club. https://workclubglobal.com/sydneys-newest-hub-of-innovation/

