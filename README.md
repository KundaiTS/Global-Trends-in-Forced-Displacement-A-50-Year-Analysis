# Global Trends in Forced Displacement A 50 Year Analysis

This project aims to analyze the UNHCR dataset on forcibly displaced populations to understand global migration and refugee trends over a 50-year period. I will leverage SQL for data preprocessing and analysis and Power BI for creating data visualizations and insights. Through the lens of SQL and PowerBI, I aim to uncover insightful patterns, explore demographic trends, and assess the causes and consequences of refugee movements across the world. The project will culminate in the creation of interactive maps and visualizations, providing a comprehensive understanding of this humanitarian challenge.

## Project Impact
This project aims to shed light on one of the world's most pressing humanitarian challenges. By analyzing the UNHCR Refugee Dataset and creating impactful visualizations, we seek to raise awareness about the causes and consequences of refugee crises, inform policymakers, and support organizations dedicated to addressing this issue. Ultimately, the project contributes to a better understanding of the global refugee landscape, with the potential to drive informed decisions and solutions for those affected by displacement.


## Data Acquisition and Exploration:

The dataset used in this analysis is the UNHCR dataset on forcibly displaced populations from the official United Nations High Commissioner for Refugees (UNHCR) website. This dataset is current and was last updated on 14 June 2023 as of the date and time of it’s download, 20/09/2023 22:18 UTC. Some data presented may differ from statistics published previously due to retroactive changes or the inclusion of previously unavailable data.	The data table contains the following:	

- Population figures
- Content type:	End-year stock population totals
- Data quick link	https://www.unhcr.org/refugee-statistics/download/?url=xXst2S
- Data definitions: https://www.unhcr.org/refugee-statistics/methodology/
- Usage license:	https://creativecommons.org/licenses/by/4.0/
- Population types included: REF, ROC, ASY, IDP, IOC, OIP, OOC, STA
- Years:	1972-2022
- Countries: All countries of asylum and all countries of origin.

I began by exploring the UNHCR Refugee Dataset to understand its structure, variables, and the breadth of data available for analysis. The following was observed

```{sql}
SELECT * FROM `unhcr displacement`;
```


##  Data Preprocessing and Cleaning:

I create a SQL database, in My SQL to store the dataset. I proceeded to load the dataset into the database. I performed Perform data cleaning, including handling missing values and duplicates.

_**Check Structure and datatypes of dataset**_

```{sql}
DESCRIBE `unhcr displacement`;
```

_**Replacing "-" in the `Other people in need of international protection` column with int values**_

```{sql}
UPDATE `unhcr displacement`
SET `Other people in need of international protection` = 0
WHERE `Other people in need of international protection` = "-";
```

_**Changing Data Type**_

```{sql}
ALTER TABLE `unhcr displacement`
MODIFY COLUMN `Other people in need of international protection` int;
```

## Data Analysis
For data cleaning, transformation, and initial querying of the UNHCR Refugee Dataset, I used SQL. I proceeded to use PowerBI to create interactive maps, dashboards, and visualizations that convey the project's insights effectively. Here are the questions I explored with this dataset:

### Trends in Global Forced Displacement
1. What is the total number of forcibly displaced people in the last 50 years?
2. Has the total number of displaced people increased or decreased over the years?
3. How has the total number of refugees, asylum seekers, and internally displaced persons (IDPs) changed over the years?
4. Which year had the highest and lowest total number of people displaced?

### Geospatial Analysis 
5. Which countries have been major sources of refugees, and which have been major host countries?
6. Have there been significant changes in the leading countries of origin and asylum over time?
7. Are there any countries that have consistently received refugees and asylum seekers while also being major sources of displacement themselves?


### Comparative Analysis and Impact of Events
8. Are there any countries where asylum seekers significantly outnumber refugees?
9. In which year did each country have the highest number of displaced people?
10. Are there specific global events, conflicts, or crises that correlate with significant changes in refugee movements or demographics?

### Other People in Need of International Protection
11. Are there specific years or periods where other people in need of international protection experienced significant increases or decreases?
12. Are there countries or regions where the proportion of "Other People in Need of International Protection" is significantly higher or lower compared to refugees and asylum seekers?

### Predictive Modeling
13. Can you build predictive models to forecast future trends in refugee movements or asylum applications based on historical data?

## Data Analysis

### Trends in Global Forced Displacement

_**What is the total number of Forcibly displaced People in the last 50 years?**_

```{sql}
SELECT
    SUM(`Refugees under UNHCRs mandate` + `Asylum seekers` + `IDPs of concern to UNHCR` + `Other people in need of international protection`) AS Total_displaced
FROM `unhcr displacement`
ORDER BY Total_displaced DESC;
```

_**Has the total number of displaced people increased of decreased over the years?**_

``` {sql}
SELECT Year,
    SUM(`Refugees under UNHCRs mandate` + `Asylum seekers` + `IDPs of concern to UNHCR` + `Other people in need of international protection`) AS Total_displaced
FROM `unhcr displacement`
GROUP BY Year
ORDER BY Total_displaced DESC;
```

_**How has the total number of refugees, asylum seekers, and internally displaced persons (IDPs) changed over the years?**_

```{sql}
SELECT Year,
    SUM(`Refugees under UNHCRs mandate`) AS Total_refugees,
    SUM(`Asylum seekers`) AS Total_asylum_seekers,
    SUM(`IDPs of concern to UNHCR`) AS Total_idps,
    SUM(`Other people in need of international protection`) AS Total_other_people_in_need
FROM `unhcr displacement`
GROUP BY Year
ORDER BY Year;
```

_**Which year had the highest and lowest total number of refugees, asylum seekers and IDPs**_

```{sql}
-- Highest Total Displaced --
SELECT Year,
    SUM(`Refugees under UNHCRs mandate` + `Asylum seekers` + `IDPs of concern to UNHCR` + `Other people in need of international protection`) AS Highest_total_displaced
FROM `unhcr displacement`
GROUP BY Year
ORDER BY Highest_total_displaced DESC
LIMIT 1;
```

```{sql}
-- Lowest Total Displaced --
SELECT Year,
    SUM(`Refugees under UNHCRs mandate` + `Asylum seekers` + `IDPs of concern to UNHCR` + `Other people in need of international protection`) AS Lowest_total_displaced
FROM `unhcr displacement`
GROUP BY Year
ORDER BY Lowest_total_displaced ASC
LIMIT 1;
```


### Geospatial Analysis

_**Which countries are major sources of refugees?**_

```{sql}
SELECT
    `Country of origin`,
    SUM(`Refugees under UNHCRs mandate`) AS Total_Refugees
FROM
    `unhcr displacement`
GROUP BY
    `Country of origin`
ORDER BY
   Total_Refugees  DESC 
LIMIT 10;
```

_**Which countries are the major hosts of asylum seekers?**_

```{sql}
SELECT 
	`Country of asylum`,
SUM(`Asylum seekers`) AS Total_Asylum_Seekers
FROM 
	`unhcr displacement`
GROUP BY 
	`Country of asylum`
ORDER BY 
	Total_Asylum_Seekers DESC
    LIMIT 10;
```

_**Have there been significant changes in the leading countries of origin and asylum over time?**_

```{sql}
WITH CountryOriginCounts AS (
  SELECT
    Year,
    `Country of origin`,
    SUM(`Refugees under UNHCRs mandate`) AS Total_Refugees
  FROM
    `unhcr displacement`
  GROUP BY
    Year,
    `Country of origin`
),
CountryAsylumCounts AS (
  SELECT
    Year,
    `Country of asylum`,
    SUM(`Refugees under UNHCRs mandate`) AS Total_Asylum
  FROM
    `unhcr displacement`
  GROUP BY
    Year,
    `Country of asylum`
),
RankedCountryOrigin AS (
  SELECT
    Year,
    `Country of origin`,
    Total_Refugees,
    ROW_NUMBER() OVER (PARTITION BY Year ORDER BY Total_Refugees DESC) AS Rank_Origin
  FROM
    CountryOriginCounts
),
RankedCountryAsylum AS (
  SELECT
    Year,
    `Country of asylum`,
    Total_Asylum,
    ROW_NUMBER() OVER (PARTITION BY Year ORDER BY Total_Asylum DESC) AS Rank_Asylum
  FROM
    CountryAsylumCounts
)
SELECT
  R1.Year,
  R1.`Country of origin` AS Leading_Origin,
  R2.`Country of origin` AS Previous_Leading_Origin,
  R1.Total_Refugees AS Leading_Origin_Count,
  R2.Total_Refugees AS Previous_Leading_Origin_Count,
  R3.`Country of asylum` AS Leading_Asylum,
  R4.`Country of asylum` AS Previous_Leading_Asylum,
  R3.Total_Asylum AS Leading_Asylum_Count,
  R4.Total_Asylum AS Previous_Leading_Asylum_Count
FROM
  RankedCountryOrigin R1
LEFT JOIN
  RankedCountryOrigin R2
ON
  R1.Year = R2.Year + 1
  AND R1.Rank_Origin = 1
  AND R2.Rank_Origin = 1
LEFT JOIN
  RankedCountryAsylum R3
ON
  R1.Year = R3.Year
  AND R1.Rank_Origin = 1
LEFT JOIN
  RankedCountryAsylum R4
ON
  R1.Year = R4.Year + 1
  AND R3.Rank_Asylum = 1
  AND R4.Rank_Asylum = 1
WHERE
  R2.`Country of origin` IS NOT NULL
  OR R4.`Country of asylum` IS NOT NULL
ORDER BY
  R1.Year;
```

_**Are there any countries that have consistently received refugees and asylum seekers while also being major sources of displacement themselves?**_

```{sql}
WITH CountryDisplacement AS (
  SELECT
    Year,
    `Country of origin`,
    SUM(`Refugees under UNHCRs mandate`) AS Total_Origin_Refugees,
    SUM(`Asylum seekers`) AS Total_Origin_Asylum_Seekers,
    SUM(`IDPs of concern to UNHCR`) AS Total_Origin_IDPs
  FROM
    `unhcr displacement`
  GROUP BY
    Year,
    `Country of origin`
),
CountryAsylum AS (
  SELECT
    Year,
    `Country of asylum`,
    SUM(`Refugees under UNHCRs mandate`) AS Total_Asylum_Refugees,
    SUM(`Asylum seekers`) AS Total_Asylum_Asylum_Seekers
  FROM
    `unhcr displacement`
  GROUP BY
    Year,
    `Country of asylum`
)
SELECT
  CO.`Country of origin` AS Country_of_Displacement,
  CA.`Country of asylum` AS Country_of_Reception,
  SUM(CO.Total_Origin_Refugees) AS Total_Origin_Refugees,
  SUM(CO.Total_Origin_Asylum_Seekers) AS Total_Origin_Asylum_Seekers,
  SUM(CO.Total_Origin_IDPs) AS Total_Origin_IDPs,
  SUM(CA.Total_Asylum_Refugees) AS Total_Asylum_Refugees,
  SUM(CA.Total_Asylum_Asylum_Seekers) AS Total_Asylum_Asylum_Seekers
FROM
  CountryDisplacement CO
JOIN
  CountryAsylum CA
ON
  CO.Year = CA.Year
  AND CO.`Country of origin` = CA.`Country of asylum`
GROUP BY
  CO.`Country of origin`,
  CA.`Country of asylum`
HAVING
  SUM(CO.Total_Origin_Refugees) > 0
  AND SUM(CO.Total_Origin_Asylum_Seekers) > 0
  AND SUM(CA.Total_Asylum_Refugees) > 0
  AND SUM(CA.Total_Asylum_Asylum_Seekers) > 0
ORDER BY
  Total_Origin_Refugees DESC;
```

##  Comparative Analysis and Impact of Events

_**Are there any countries where asylum seekers significantly outnumber refugees?**_
```{sql}
SELECT `Country of origin`, 
SUM(`Asylum seekers`) AS Total_Asylum_Seekers, 
SUM(`Refugees under UNHCRs mandate`) AS Total_Refugees
FROM `unhcr displacement`
GROUP BY `Country of origin`
HAVING Total_Asylum_Seekers > Total_Refugees
ORDER BY Total_Asylum_Seekers DESC;
```

_**In which year did each country have the highest number of displaced people?**_

```{sql}
WITH DisplacementSummary AS (
    SELECT
        `Country of origin`,
        Year,
        SUM(`Refugees under UNHCRs mandate`) AS Total_Refugees,
        SUM(`Asylum seekers`) AS Total_Asylum_Seekers,
        SUM(`IDPs of concern to UNHCR`) AS Total_IDPs,
        SUM(`Other people in need of international protection`) AS Total_Other_Needs
    FROM
        `unhcr displacement`
    GROUP BY
        `Country of origin`,
        Year
),
MaxDisplacementYear AS (
    SELECT
        `Country of origin`,
        MAX(Total_Refugees + Total_Asylum_Seekers + Total_IDPs + Total_Other_Needs) AS Max_Displacement
    FROM
        DisplacementSummary
    GROUP BY
        `Country of origin`
)
SELECT
    m.`Country of origin`,
    d.Year AS Year_of_Highest_Displacement,
    Max_Displacement
FROM
    DisplacementSummary d
JOIN
    MaxDisplacementYear m
ON
    d.`Country of origin` = m.`Country of origin`
    AND (d.Total_Refugees + d.Total_Asylum_Seekers + d.Total_IDPs + d.Total_Other_Needs) = m.Max_Displacement
ORDER BY
   Max_Displacement DESC;
```


## Other People in Need of International Protection 

_**Are there specific years or periods where other people in need of international protection experienced significant increases or decreases?**_

```{sql}
WITH OtherProtectionChanges AS (
    SELECT
        Year,
        SUM(`Other people in need of international protection`) AS Total_Other_Protection
    FROM
        `unhcr displacement`
    GROUP BY
        Year
)
SELECT
    Year,
    Total_Other_Protection,
    LAG(Total_Other_Protection) OVER (ORDER BY Year) AS Previous_Year_Other_Protection,
    (Total_Other_Protection - LAG(Total_Other_Protection) OVER (ORDER BY Year)) AS Change_In_Other_Protection
FROM
    OtherProtectionChanges
ORDER BY
    Year;
```

_**Are there countries or regions where the proportion of "Other People in Need of International Protection" is significantly higher or lower compared to refugees and asylum seekers?**_

```{sql}
WITH ProtectionComparison AS (
    SELECT
        Year,
        `Country of origin`,
        SUM(`Other people in need of international protection`) AS Total_Other_Protection,
        SUM(`Refugees under UNHCRs mandate`) AS Total_Refugees,
        SUM(`Asylum seekers`) AS Total_Asylum_Seekers
    FROM
        `unhcr displacement`
    GROUP BY
        Year,
        `Country of origin`
)
SELECT
    Year,
    `Country of origin`,
    Total_Other_Protection,
    Total_Refugees,
    Total_Asylum_Seekers,
    CASE
        WHEN Total_Other_Protection > (Total_Refugees + Total_Asylum_Seekers) THEN 'Higher'
        WHEN Total_Other_Protection < (Total_Refugees + Total_Asylum_Seekers) THEN 'Lower'
        ELSE 'Equal'
    END AS Proportion_Comparison
FROM
    ProtectionComparison
ORDER BY
    Year,
    `Country of origin`;
```
