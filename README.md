# Investigating Pesticide Usage and Cancer Rates in Glenn County, California

## Description:

This data analysis project aims to explore the potential relationship between pesticide usage in agriculture fields surrounding the town of Glenn County, California, and the high cancer rates in the area. Willows has been experiencing elevated cancer rates, and this investigation seeks to determine if there is any correlation between the types of pesticides used in the agriculture fields and the incidence of specific types of cancer.

## Project Objectives:

1. Analyze pesticide usage data in the agriculture fields in Glenn County including the types and quantities of pesticides used.
   
2. Examine cancer incidence data in the town and neighboring areas, identifying the prevalence of different cancer types.
   
3. Investigate the chemical composition of pesticides to identify known cancer-causing ingredients.
   
4. Perform statistical analysis to assess the relationship between pesticide usage and cancer rates.
   
5. Conduct geospatial analysis to visualize and understand the spatial distribution of pesticide usage and cancer rates in the region.
    
6. Provide insights and recommendations based on the analysis findings to raise awareness and inform potential public health interventions.
    

## Data Sources:

- Pesticide usage data from local agricultural departments or regulatory agencies.
  
- Cancer incidence data from local health departments or cancer registries.

- [Dataset 1] (https://wiki.cancer.org.au/policy/Position_statement_-_Pesticides_and_cancer#app1):

The data offers a comprehensive position statement on the potential relationship between pesticides and the development of cancer. This authoritative stance is presented on the Cancer Council Australia's wiki platform.

The webpage provides a thorough examination of the Cancer Council's viewpoint, likely incorporating scientific research, evidence, and informed analysis related to the impact of pesticide exposure on cancer risk. The inclusion of an appendix, denoted by "app1" in the URL, suggests the presence of supplementary resources or additional information that supports or elaborates on the main position statement.

This dataset holds significant value for researchers, policymakers, healthcare professionals, and individuals interested in understanding the scientific consensus and expert opinion regarding the connection between pesticides and cancer. The dataset's reliability, affiliation with a reputable institution, and potential inclusion of references make it a credible and informative resource for those seeking to delve into the complex relationship between pesticide usage and its potential health implications.

- [Dataset 2] (https://www.californiahealthmaps.org/?areatype=county&address=021&sex=Both&site=AllSite&race=&year=05yr&overlays=labels&choropleth=AAIR):

Data hosted on the California Health Maps platform. This dataset is focused on providing comprehensive health statistics within the context of California counties.

The dataset encompasses a diverse array of health-related information, potentially including disease incidence rates, demographics, and other health indicators. By utilizing the provided URL parameters, users can tailor the dataset to their specific needs, filtering data by attributes such as sex, site, race, and year. The presence of the choropleth visualization ("choropleth=AAIR") indicates that the dataset's data presentation is enhanced through color-coded maps. This visual approach facilitates the identification of trends and disparities in health metrics across various counties in California.

Researchers, healthcare professionals, policymakers, and analysts can leverage this dataset to gain insights into health patterns, make informed decisions, and conduct in-depth analyses at the county level. The dataset's emphasis on geographical granularity and customizable filters contributes to its potential for informing public health research, resource allocation, and policy formulation.
  
- [Dataset 3] (https://www.cdpr.ca.gov/docs/emon/grndwtr/plss_shapefiles.htm):

The source provides shapefiles pertaining to the Public Land Survey System (PLSS). PLSS is a framework used for surveying land parcels in the United States, offering a systematic method for parcel identification and management. These shapefiles encompass geographic data that define the boundaries and divisions of land parcels within the PLSS structure.

This dataset, sourced from the California Department of Pesticide Regulation (CDPR), holds potential significance for tasks such as land management, environmental analysis, and geographic information system (GIS) projects. Researchers, analysts, and land administrators can leverage this dataset to gain insights into land parcel delineation, spatial planning, and related areas of study. The dataset contributes valuable geographic context that can aid in decision-making processes and contribute to a deeper understanding of land utilization patterns.

## Methods and Tools:

- Microsoft SQL Server Management Studio & Microsoft Excel for data manipulation and cleaning.

- Statistical analysis using appropriate methods, such as correlation analysis and regression.

- Tableau for visualization.


## Disclaimer:

This repository contains the code and documentation for the entire data analysis process, including data collection, cleaning, preprocessing, exploratory data analysis (EDA), statistical analysis, geospatial visualization, and interpretation of results. The project also includes a literature review to identify cancer-causing ingredients in pesticides used in the area. This analysis is conducted by a beginner data analyst, and the results should be interpreted with caution. The project aims to foster discussion and provide valuable learning experiences in data analysis techniques and environmental health research. Within the Office of Pesticide Programs, the Health Effects Division conducts an independent assessment of studies carried out on mice and rats. This evaluation aims to determine the potential of pesticides to induce cancer. The conclusions drawn from this unbiased analysis undergo a peer review process led by the Cancer Assessment Review Committee, a panel of qualified experts. Based on their evaluation, the committee proposes a cancer classification. This classification has a significant bearing on how the pesticide is regulated by the Agency, outlining strategies to quantify the level of risk posed to humans. Additionally, there are instances where the EPA seeks supplementary evaluation from the FIFRA Scientific Advisory Panel.

## Note:

The dataset used in this analysis may be subject to data privacy and usage restrictions. Therefore, it is not suitable for making individual health-related conclusions.

# 1. Analyzing Pesticide Usage in Agriculture Fields in Glenn County, California

Loaded the chemicals datasets from the years 2016-2021. I then combined all the tables into a single table named, 'Chemical_AllYears'.

```sql
SELECT TOP (1000) [Year]
      ,[County]
      ,[Chemical]
      ,[Commodity]
      ,[Pounds]
      ,[Num_Apps]
      ,[Area_treated]
      ,[Unit_treated]
  FROM [Cancer_Study].[dbo].[Chemical_2016]
   
-------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------

--Combining all the chemical tables

SELECT* 
Into Chemical_AllYears
FROM (
	SELECT * FROM Chemical_2016
	UNION ALL
	SELECT * FROM Chemical_2017
	UNION ALL
	SELECT * FROM Chemical_2018
	UNION ALL
	SELECT * FROM Chemical_2019
	UNION ALL
	SELECT * FROM Chemical_2020
	UNION ALL
	SELECT * FROM Chemical_2021
) AS AllChemicals;
```
</pre>

Loaded the commodity datasets from the years 2016-2021. I then combined all the commodity tables into a single table named, 'Commodity_AllYears'.

```sql
SELECT TOP (1000) [Year]
      ,[County]
      ,[Commodity]
      ,[Chemical]
      ,[Pounds]
      ,[Num_Apps]
      ,[Area_Treated]
      ,[Unit_treated]
  FROM [Cancer_Study].[dbo].[Commodity_2016]
   
-------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------

-- Combining all the commodity tables

SELECT *
INTO Commodity_AllYears
FROM (
    SELECT * FROM Commodity_2016
    UNION ALL
    SELECT * FROM Commodity_2017
    UNION ALL
    SELECT * FROM Commodity_2018
    UNION ALL
    SELECT * FROM Commodity_2019
    UNION ALL
    SELECT * FROM Commodity_2020
    UNION ALL
    SELECT * FROM Commodity_2021
) AS AllCommodities;
```
</pre>

Combining 'Chemical_AllYears' and 'Commodities_AllYears'. Both tables had similar columns. 

```sql
  -- Created the new table

CREATE TABLE [Cancer_Study].[dbo].[Chemical_And_Commodity_2016_2021] (
    Chemical VARCHAR(255),
    Commodity VARCHAR(255),
    Pounds DECIMAL(18, 2),
    Num_Apps INT,
    Area_treated DECIMAL(18, 2)
);

-- Insert data from Chemical_AllYears

INSERT INTO [Cancer_Study].[dbo].[Chemical_And_Commodity_2016_2021]
(Chemical, Commodity, Pounds, Num_Apps, Area_treated)
SELECT Chemical, Commodity, Pounds, Num_Apps, Area_treated
FROM [Cancer_Study].[dbo].[Chemical_AllYears]
WHERE Year BETWEEN 2016 AND 2021;

-- Insert data from Commodity_AllYears

INSERT INTO [Cancer_Study].[dbo].[Chemical_And_Commodity_2016_2021]
(Chemical, Commodity, Pounds, Num_Apps, Area_treated)
SELECT Chemical, Commodity, Pounds, Num_Apps, Area_treated
FROM [Cancer_Study].[dbo].[Commodity_AllYears]
WHERE Year BETWEEN 2016 AND 2021;
```
</pre>

# INSERT VISUAL REPRESENTATION OF DATA COMBINATION

Loaded the dataset 'List of Classifications – IARC'.

```sql
SELECT TOP (1000) [CAS No#]
      ,[Agent]
      ,[Group]
      ,[Volume]
      ,[Year]
      ,[Additional information]
  FROM [Cancer_Study].[dbo].['List of Classifications – IARC $']
   
-------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------------------

-- Keeping only the 'Agent' and 'Group' column.
   
SELECT TOP (1000) [Agent], [Group]
FROM [Cancer_Study].[dbo].['List of Classifications – IARC $']
WHERE [Group] = 1;

-- Now I'm making it into a new table named, 'Cancerous_Chemicals1'.

SELECT TOP (1000) [Agent], [Group]
INTO Group_1_Carocengenic
FROM [Cancer_Study].[dbo].['List of Classifications – IARC $']
WHERE [Group] = 1;
```
</pre>

Loaded the dataset 'p65chemicallist'. Dataset that displays chemicals that have been studied. Shows us what chemicals are cancerous and which are developmental, meaning that the chemical can lead to cancer after a certain amount of exposure.

```sql
SELECT TOP (1000) [STATE OF CALIFORNIA]
      ,[F2]
      ,[F3]
      ,[F4]
      ,[F5]
      ,[F6]
  FROM [Cancer_Study].[dbo].[p65chemicalslist$]
   
------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------

--Changing column names   
   
SELECT [STATE OF CALIFORNIA] AS Chemical,
       [F2] AS [Type of Toxicity],
       [F3] AS [Listing Mechanism],
       [F4] AS [CAS No.],
       [F5] AS [Date Listed],
       [F6] AS [NSRL or MADL (µg/day)a]
FROM [Cancer_Study].[dbo].[p65chemicalslist$];

------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------

--Checking to see if it worked
   
SELECT
    [STATE OF CALIFORNIA] AS Chemical,
    [F2] AS [Type of Toxicity]
FROM [Cancer_Study].[dbo].[p65chemicalslist$];
   
 ------------------------------------------------------------------------------------------------------
 ------------------------------------------------------------------------------------------------------

-- Created the new table with an increased column length
   
CREATE TABLE [Cancer_Study].[dbo].[Cancerous_Chemicals] (
    Chemical VARCHAR(500), -- or a higher value
    [Type of Toxicity] VARCHAR(255)
);

-- Inserted the selected data into a new table
   
INSERT INTO [Cancer_Study].[dbo].[Cancerous_Chemicals] (Chemical, [Type of Toxicity])
SELECT
    [STATE OF CALIFORNIA] AS Chemical,
    [F2] AS [Type of Toxicity]
FROM [Cancer_Study].[dbo].[p65chemicalslist$];
```
</pre>

Going to filter out only the cancerous chemicals from the column titled, 'Type of Toxicity' and create a new table named, 'Cancerous_Chemicals2'

```sql
ELECT TOP (1000) [Chemical]
      ,[Type of Toxicity]
  FROM [Cancer_Study].[dbo].[Cancerous_Chemicals]

------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------

--filtering out only the cancerous chemicals
	
SELECT [Chemical], [Type of Toxicity]
INTO [Cancer_Study].[dbo].[Filtered_Cancerous_Chemicals]
FROM [Cancer_Study].[dbo].[Cancerous_Chemicals]
WHERE [Type of Toxicity] = 'cancer';

--making a new table for the cancerous chemicals

SELECT *
INTO [Cancer_Study].[dbo].[Cancerous_Chemicals2]
FROM [Cancer_Study].[dbo].[Carcinogenic];
```
</pre>

I filtered unique carcinogenic substances. 

```sql
SELECT DISTINCT [Carcinogenic_Substance]
INTO [Cancer_Study].[dbo].[Carcinogenic_Distinct]
FROM [Cancer_Study].[dbo].[Carcinogenic]
```
</pre>

Finding any values in the 'Carcinogenic_Substance' column of the 'Carcinogenic' table match with values in the 'Chemical' column of the 'Chemical_AllYears' table.

```sql
SELECT DISTINCT c.Carcinogenic_Substance
FROM Carcinogenic c
INNER JOIN Chemical_AllYears ca ON c.Carcinogenic_Substance = ca.Chemical;
```
</pre>

Created a new table named 'Matching_Chemicals' to store the chemicals that match between the 'Carcinogenic' table and the 'Chemical_AllYears' table. I wanted to match them based on the 'Carcinogenic_Substance' column in the 'Carcinogenic' table and the 'Chemical' column in the 'Chemical_AllYears' table.

```sql
-- Create the new table
	
CREATE TABLE [Cancer_Study].[dbo].[Matching_Chemicals] (
    [Chemical] VARCHAR(255) -- Change the data type if needed
);

-- Insert matching chemicals from the Carcinogenic table into the new table
INSERT INTO [Cancer_Study].[dbo].[Matching_Chemicals] ([Chemical])
SELECT ca.[Chemical]
FROM [Cancer_Study].[dbo].[Carcinogenic] car
JOIN [Cancer_Study].[dbo].[Chemical_AllYears] ca ON car.[Carcinogenic_Substance] = ca.[Chemical];
```
</pre>

After isolating Group 1 carcinogenic chemicals through careful filtering of various datasets, my next step involves the creation of a new table that presents these Group 1 carcinogenic chemicals alongside the corresponding commodities onto which they were applied. This table will also provide insights into the quantities of these chemicals used, measured in pounds, as well as the frequency of their application in terms of the number of applications.

```sql
--Creating Permament Table

CREATE VIEW [dbo].[Toxic_Chemicals_on_Commodities]
AS
SELECT cac.Chemical, cac.Commodity, cac.Pounds, cac.Num_Apps, cac.Area_treated
FROM [Chemical_And_Commodity_2016_2021] cac
INNER JOIN [Matching_Chemicals] mc ON cac.Chemical = mc.Chemical;
```
</pre>

Summarized view of the total pounds, total number of applications, and total area treated for each chemical-commodity combination.\

```sql
SELECT Chemical, Commodity, SUM(Pounds) AS Total_Pounds, SUM(Num_Apps) AS Total_Num_Apps, SUM(Area_treated) AS Total_Area_treated
FROM [Cancer_Study].[dbo].[Toxic_Chemicals_on_Commodities]
GROUP BY Chemical, Commodity;

--Creating Permament Table

CREATE VIEW [dbo].[Sum_Chem_Comm]
AS
SELECT Chemical, Commodity, SUM(Pounds) AS Total_Pounds, SUM(Num_Apps) AS Total_Num_Apps, SUM(Area_treated) AS Total_Area_treated
FROM [Cancer_Study].[dbo].[Toxic_Chemicals_on_Commodities]
GROUP BY Chemical, Commodity;
```
</pre>

# Conclusion 
Pesticides find extensive application in agriculture, various occupational settings, and homes. A subset of the chemicals present in pesticides has been associated with cancer based on findings from laboratory experiments and epidemiological studies. Nevertheless, definitive proof connecting overall pesticide usage to cancer remains inconclusive.

The diverse array of chemicals found in pesticides, combined with potential contributing factors to cancer among individuals exposed to these substances, renders it challenging to definitively establish direct connections between pesticide usage and cancer occurrences. Simultaneously, the available evidence is inadequate to conclusively ascertain the absence of any relationship between cancer and exposure to pesticides, whether through direct chemical contact or residual interactions.
