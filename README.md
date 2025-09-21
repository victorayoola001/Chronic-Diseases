# Prevalence of Chronic Diseases among the U.S population
## 📚 Table of contents
1. [Project Overview](Project-Overview)
2. [Tools & Technologies](Tools--Tchnologies)
3. [Dataset overview](Dataset-overview)
4. [Data cleaning process](Data-cleaning-process)
5. [Exploratory Data Analysis](Exploratory-Data-Analysis)
6. [PowerBi Dashboard](PowerBi-Dashboard)
7. [Key Insights](Key-Insights)
8. [Recommendations](Recommendations)
9. [Data Source](Data-Source)

## Project Overview
#### This Project seeks to explore the prevalence of various chronic diseases across different age groups in the U.S. It showcases the relationship between chronic conditions and how they affect young and older populations, while also highlighting specific risk factors that predispose each population group to these chronic conditions. 
### Tools & Technologies
1. SQL (MySQL Workbench)
2. Jupyter notebook
3. PowerBi
4. Excel
### Dataset Overview
Columns:
State,	Sex,	GeneralHealth,	PhysicalHealthDays,	MentalHealthDays,	LastCheckupTime,	PhysicalActivities,	SleepHours,	RemovedTeeth,	HadHeartAttack,	HadAngina,	HadStroke,	HadAsthma,	HadSkinCancer,	HadCOPD,	HadDepressiveDisorder, HadKidneyDisease,	HadArthritis,	HadDiabetes,	DeafOrHardOfHearing,	BlindOrVisionDifficulty,	DifficultyConcentrating,	DifficultyWalking,	DifficultyDressingBathing,	DifficultyErrands,	SmokerStatus,	ECigaretteUsage,	ChestScan,	RaceEthnicityCategory,	AgeCategory,	HeightInMeters,	WeightInKilograms,	BMI,	AlcoholDrinkers,	HIVTesting,	FluVaxLast12,	PneumoVaxEver,	TetanusLast10Tdap,	HighRiskLastYear,	CovidPos

Sample Preview (Table rows and columns were truncated for easier readability)

| State |	Sex	| GeneralHealth | PhysicalHealthDays |	MentalHealthDays | LastCheckupTime | PhysicalActivities |	SleepHours | RemovedTeeth |
|-------|-----|---------------|--------------------|-------------------|-----------------|--------------------|------------|--------------|
| Alabama |	Female | Very good |	4	|	0	|	Within past year | Yes |	9	|	None of them |
| Alabama |	Male | Very good |	0	|	0	|	Within past year | Yes |	6	|	None of them |
| Alabama |	Male|	Very good |	0	|	0	|	Within past year |	No	|	8	|	6 or more |
| Alabama | 	Female|	Fair |	5	|	0	|	Within past year |	Yes	|	9	|	None of them |
| Alabama |	Female|	Good |	3	|	15 |	Within past year |	Yes	|	5	|	1 to 5 |

### Data cleaning process
* Converted numeric and string columns to proper data type formats
* Removed invalid values
* Created new calculated fields
* Filled in missing values, and those that couldn't be filled were removed

### Exploratory Data Analysis
##### 1. What percentage of people had a heart attack?
```SQL
WITH Counts AS (
    SELECT 
        COUNT(*) AS Total_Rows,
        SUM(CASE WHEN Had_Heart_Attack = 'Yes' THEN 1 ELSE 0 END) AS Total_Yes
    FROM Heart
)
SELECT 
    Total_Yes,
    Total_Rows,
    ROUND((Total_Yes / Total_Rows) * 100,2) AS Percent_with_Heart_Attack
FROM Counts;
```
##### 2. List all individuals who have had a stroke and are smokers.
```SQL
SELECT
    State,
    Sex,
    Had_Stroke,
    Smoker_Status
FROM Heart
WHERE Had_Stroke = 'Yes'
AND Smoker_Status LIKE 'Current%'
LIMIT 10;
```
##### 3. How many people are in each age category?
```SQL
SELECT
    Age_Category,
    COUNT(Age_Category) AS Number_in_age_Category
FROM
    Heart
GROUP BY Age_Category
ORDER BY Age_Category ASC
LIMIT 10;
```
##### 4. What is the average number of days people report poor physical health?
```SQL
SELECT
    ROUND(AVG(Physical_Health_Days),2) AS AVG_No_of_Days
FROM
    Heart
WHERE
    General_Health = 'Poor';
```
##### 5. How many people are there in each race/ethnicity category?
```SQL
SELECT
    race_ethnicity_category,
    Count(race_ethnicity_category) AS No_of_people_per_ethnicity
FROM
    Heart
GROUP BY
    race_ethnicity_category;
```
##### 6. What is the average number of sleep hours for people who have had asthma?
```SQL
SELECT
    ROUND(AVG(Sleep_Hours),2) AS Average_Sleep_Hours
FROM
    Heart
WHERE 
    Had_Asthma = 'Yes'
```
##### 7. What is the percentage of people who had high risk conditions last year?
```SQL
SELECT
    ROUND((SUM(CASE WHEN High_Risk_Last_year = 'Yes'
             THEN 1 ELSE 0 END)/COUNT(*)) * 100,2) AS Percent_of_people_with_HRLY
FROM
    Heart;
```
##### 8. How many individuals report difficulty in walking?
```SQL
SELECT
    Count(Difficulty_Walking) As Number_With_Walking_Difficulty
FROM
    Heart
WHERE
    Difficulty_Walking = 'Yes'
```
##### 9. List the top 5 most common chronic conditions based on the number of occurrences.
```SQL
SELECT condition_name, total_yes
FROM (
    SELECT 'Heart_Attack' AS condition_name, 
           SUM(CASE WHEN Had_Heart_Attack = 'Yes' THEN 1 ELSE 0 END) AS total_yes 
    FROM Heart
    UNION ALL
    SELECT 'Angina', SUM(CASE WHEN Had_Angina = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'Stroke', SUM(CASE WHEN Had_Stroke = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'Asthma', SUM(CASE WHEN Had_Asthma = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'Skin_Cancer', SUM(CASE WHEN Had_Skin_Cancer = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'COPD', SUM(CASE WHEN Had_COPD = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'Depressive_Disorder', SUM(CASE WHEN Had_Depressive_Disorder = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'Kidney_Disease', SUM(CASE WHEN Had_Kidney_Disease = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'Arthritis', SUM(CASE WHEN Had_Arthritis = 'Yes' THEN 1 ELSE 0 END) FROM Heart
    UNION ALL
    SELECT 'Diabetes', SUM(CASE WHEN Had_Diabetes = 'Yes' THEN 1 ELSE 0 END) FROM Heart
) AS condition_counts
ORDER BY total_yes DESC
LIMIT 5;
```
##### 10. What is the average age of individuals who have had a heart attack?
```SQL
SELECT 
    AVG(
        CASE 
            WHEN Age_Category = 'Age 18 to 24' THEN 21
            WHEN Age_Category = 'Age 25 to 29' THEN 27
            WHEN Age_Category = 'Age 30 to 34' THEN 32
            WHEN Age_Category = 'Age 35 to 39' THEN 37
            WHEN Age_Category = 'Age 40 to 44' THEN 42
            WHEN Age_Category = 'Age 45 to 49' THEN 47
            WHEN Age_Category = 'Age 50 to 54' THEN 52
            WHEN Age_Category = 'Age 55 to 59' THEN 57
            WHEN Age_Category = 'Age 60 to 64' THEN 62
            WHEN Age_Category = 'Age 65 to 69' THEN 67
            WHEN Age_Category = 'Age 70 to 74' THEN 72
            WHEN Age_Category = 'Age 75 to 79' THEN 77
            WHEN Age_Category = 'Age 80 or older' THEN 82 
        END
    ) AS Estimated_Avg_Age
FROM Heart
WHERE Had_Heart_Attack = 'Yes';
```
##### 11. How many people reported having a depressive disorder and also drink alcohol?
```SQL
SELECT
    Sum(CASE WHEN Had_depressive_disorder = 'YES' AND Alcohol_drinkers = 'YES' THEN 1 ELSE 0 END) AS Total_depressive_Alcoholics
FROM 
    Heart;
```
##### 12. What is the average number of physical activity days for people who are smokers?
```SQL
SELECT
    ROUND(AVG(Physical_Health_Days),2)
FROM
    Heart
WHERE
    Physical_Activities = 'Yes'
AND
    Smoker_Status LIKE 'Current%'
```
##### 13. Which individuals have both high BMI and have had a stroke?
```SQL
SELECT
    Sex,    
    Had_Stroke,
    BMI
FROM
    Heart
WHERE
    Had_Stroke = 'Yes'
AND
    BMI > 25
ORDER BY BMI DESC
LIMIT 10;
```
##### 14. How many people have had an HIV test in the last 12 months(from the last date?
```SQL
SELECT
    COUNT(HIV_Testing) AS No_of_HIV_Test
FROM
    Heart
WHERE
    HIV_Testing = 'Yes'
AND
    Last_Checkup_Time LIKE 'Within past year%'
```
##### 15. Find the average number of days of poor mental health for each race/ethnicity category.
```SQL
SELECT
    Race_ethnicity_Category,
    ROUND(AVG(Mental_Health_Days),2) AS AVG_Days_Mental_Health
FROM
    Heart
GROUP BY 
    Race_ethnicity_Category
```
##### 16. What percentage of people with arthritis also have difficulty dressing or bathing?
```SQL
SELECT
    ROUND(SUM(CASE WHEN Had_Arthritis = 'YES' 
              AND Difficulty_Dressing_Bathing = 'YES' THEN 1 ELSE 0 END)/
              (SUM(CASE WHEN Had_Arthritis = 'YES' THEN 1 ELSE 0 END))*100,1) AS Percent_ADB
FROM
    Heart
```

### PowerBi Dashboard
The powerbi dashboard includes an overview report page showing distribution analysis for:
- 📊 Angina
- 📉 Arthritis
- 💹 Asthma
- 📊 Stroke
- 📉 COPD
- 📈 Depressive Disorder
- 💹 Diabetes
- 📈 Heart Attack
- 📊 Kidney Disease
- 📉 Skin Cancer

![Screenshot 2025-08-22 215108](https://github.com/user-attachments/assets/236c9551-2080-4dc0-add5-1379a87fa27a)

### Key Insights
- The age group with the highest population based on sample size is 65-69
- The highest ethnic race represented is white only, non-Hispanic
- Arthritis, Depressive Disorder, and Asthma are the most common chronic conditions in the US Population
- These conditions are more common in females than males
- The least common chronic condition is stroke, as only 4% of the population reported having or having had a stroke
- The elderly population 80 years and older is more affected by stroke
- The most common condition among the younger population between 18 and 34 years is depressive disorder
- 5% of the entire population has had a heart attack
- 7% of people with Arthritis also reported having difficulty in dressing and walking

### Recommendation(s)
- More preventive interventions should be targeted towards the three most common chronic conditions in the U.S
- More research should be done to discover risk factors that make females more predisposed to these conditions
- The Geriatric population requires special neurological attention and should be factored into their basic care
- Personalized therapy sessions should be mandated for the younger population, 18 to 34 years, based on individual needs

Dataset Source

[Download Here](https://drive.google.com/drive/folders/1Z3dWTERJcGgzfSoDxBbKwgJTqXyxRXet?usp=sharing)
    
