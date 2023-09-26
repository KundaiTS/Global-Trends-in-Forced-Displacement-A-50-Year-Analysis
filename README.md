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
1. What is the total number of Forcibly displaced People in the last 50 years?
2. Has the total number of displaced people increased of decreased over the years?
3. How has the total number of refugees, asylum seekers, and internally displaced persons (IDPs) changed over the years?
4. Which year had the highest and lowest total number of refugees, asylum seekers and IDPs

### Geospatial Analysis and Impact of Events
5. Which countries have been major sources of refugees, and which have been major host countries?
6. Have there been significant changes in the leading countries of origin and asylum over time?
7. Are there any countries that have consistently received refugees and asylum seekers while also being major sources of displacement themselves?
8. Are there specific global events, conflicts, or crises that correlate with significant changes in refugee movements or demographics?

### Comparative Analysis
9. How do the numbers of refugees, asylum seekers, and IDPs compare to one another over the years?
10. Are there any countries where asylum seekers significantly outnumber refugees?

### Other People in Need of International Protection
11. What are the trends in the number of internally displaced persons (IDPs) of concern to UNHCR?
12. Which countries have the highest number of IDPs of concern, and have these numbers increased or decreased?


### Predictive Modeling
15. Can you build predictive models to forecast future trends in refugee movements or asylum applications based on historical data?

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

_**Which country of origin has the highest number of displaced people?**_ 

```{sql}
SELECT
    `Country of origin`,
    SUM(`Refugees under UNHCRs mandate` + `Asylum seekers` + `IDPs of concern to UNHCR` + `Other people in need of international protection`) AS Total_Displaced_People
FROM
    `unhcr displacement`
GROUP BY
    `Country of origin`
ORDER BY
    Total_Displaced_People DESC; 
```

_**Which country of origin has the lowest number of displaced people?**_

```{sql}

SELECT
    `Country of origin`,
    SUM(`Refugees under UNHCRs mandate` + `Asylum seekers` + `IDPs of concern to UNHCR` + `Other people in need of international protection`) AS Total_Displaced_People
FROM
    `unhcr displacement`
GROUP BY
    `Country of origin`
ORDER BY
    Total_Displaced_People ASC 
LIMIT 1;
```

### Country-Specific Analysis

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

_**Are there any countries that have consistently received refugees and asylum seekers while also being major sources of displacement themselves?**_

```{sql}
WITH CountryDisplacement AS (
    SELECT
        `Country of origin`,
        `Country of asylum`,
        SUM(`Refugees under UNHCRs mandate` + `Asylum seekers`) AS Total_Outbound_Displacement
    FROM
        `unhcr displacement`
    GROUP BY
        `Country of origin`,
        `Country of asylum`
),
CountryReception AS (
    SELECT
        `Country of origin`,
        `Country of asylum`,
        SUM(`Refugees under UNHCRs mandate` + `Asylum seekers`) AS Total_Inbound_Displacement
    FROM
        `unhcr displacement`
    GROUP BY
        `Country of asylum`,
        `Country of origin`
)
SELECT
    A.`Country of origin`,
    A.`Country of asylum`
FROM
    CountryDisplacement A
JOIN
    CountryReception B
ON
    A.`Country of origin` = B.`Country of origin`
WHERE
    A.Total_Outbound_Displacement > 0
    AND B.Total_Inbound_Displacement > 0;
```

_**In which year did each country have the highest Refugees, and Asylum Seekers**_

```{sql}
WITH max_values AS (
  SELECT
    `Country of origin`,
    MAX(`Refugees under UNHCRs mandate`) AS max_refugees,
    MAX(`Asylum seekers`) AS max_asylum_seekers
  FROM
    `unhcr displacement`
  GROUP BY
    `Country of origin`
)

SELECT
  u.`Country of origin`,
  u.Year AS Year_Refugees,
  u.Year AS Year_Asylum_seekers
FROM
  `unhcr displacement` u
Inner JOIN
  max_values max_ref ON u.`Country of origin` = max_ref.`Country of origin` AND u.`Refugees under UNHCRs mandate` = max_ref.max_refugees
inner JOIN
  max_values max_asylum ON u.`Country of origin` = max_asylum.`Country of origin` AND u.`Asylum seekers` = max_asylum.max_asylum_seekers;
``` 

### Comparative Analysis

_**How do the numbers of refugees, asylum seekers, and IDPs compare to one another over the years?**_

```{sql}
SELECT
Year,
    SUM(`Refugees under UNHCRs mandate`) AS Total_Refugees,
    SUM(`Asylum seekers`) AS Total_Asylum_Seekers,
    SUM(`IDPs of concern to UNHCR`) AS Total_IDPs,
    SUM(`Other people in need of international protection`) AS Total_Other_In_Need
FROM
    `unhcr displacement`
GROUP BY
    Year
ORDER BY
    Year;
```

_**Are there any countries where asylum seekers significantly outnumber refugees?**_
```{sql}
SELECT
    Year,
    `Country of origin`,
    SUM(`Asylum seekers`) AS Total_Asylum_Seekers,
    SUM(`Refugees under UNHCRs mandate`) AS Total_Refugees,
    CASE
        WHEN SUM(`Refugees under UNHCRs mandate`) = 0 THEN 0
        ELSE SUM(`Asylum seekers`) / SUM(`Refugees under UNHCRs mandate`)
    END AS Asylum_Seeker_to_Refugee_Ratio
FROM
    `unhcr displacement`
GROUP BY
    Year,
    `Country of origin`
HAVING
    Asylum_Seeker_to_Refugee_Ratio > 2; -- You can adjust the threshold as needed
```

## IDPs of Concern to UNHCR

_**What are the trends in the number of internally displaced persons (IDPs) of concern to UNHCR?**_

```{sql}
SELECT
    Year,
    SUM(`IDPs of concern to UNHCR`) AS Total_IDPs
FROM
    `unhcr displacement`
GROUP BY
    Year
ORDER BY
    Year;
```

_**Which countries have the highest number of IDPs of concern, and have these numbers increased or decreased?**_

```{sql}
SELECT
    `Country of asylum`,
    MAX(`IDPs of concern to UNHCR`) AS Highest_IDPs,
    MIN(`IDPs of concern to UNHCR`) AS Lowest_IDPs,
    MAX(`IDPs of concern to UNHCR`) - MIN(`IDPs of concern to UNHCR`) AS Change_in_IDPs
FROM
    `unhcr displacement`
GROUP BY
    `Country of asylum`
ORDER BY
    Highest_IDPs DESC;
```














