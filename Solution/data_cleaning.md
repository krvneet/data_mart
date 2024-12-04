# Data Mart Case Study - Data Cleaning

## Overview

This repository contains SQL solutions for the **Data Mart Case Study**, focusing on cleaning and transforming the `data_mart.weekly_sales` table. The goal of this data cleaning process is to prepare the dataset for further analysis, ensuring that all necessary fields are formatted, categorized, and enriched for effective insights.

---

## Data Cleansing Steps

The SQL query transforms the `data_mart.weekly_sales` table into a new table named **`clean_weekly_sales`** by performing the following steps:

### Operations Performed:

| **Column Name**        | **Actions Performed**                                                                                      |
|-------------------------|----------------------------------------------------------------------------------------------------------|
| `week_date`            | Converted to `DATE` format using `TO_DATE`.                                                              |
| `week_number`*         | Extracted week number using `EXTRACT(WEEK FROM ...)`.                                                    |
| `month_number`*        | Extracted month number using `EXTRACT(MONTH FROM ...)`.                                                  |
| `calendar_year`*       | Extracted year using `EXTRACT(YEAR FROM ...)`.                                                           |
| `region`               | No changes required.                                                                                     |
| `platform`             | No changes required.                                                                                     |
| `segment`              | Null values replaced with `"unknown"`.                                                                   |
| `customer_type`        | No changes required.                                                                                     |
| `age_band`*            | Categorized using `CASE WHEN`:                                                                           |
|                        | - `1` = **Young Adults**                                                                                 |
|                        | - `2` = **Middle Aged**                                                                                  |
|                        | - `3` or `4` = **Retirees**                                                                              |
|                        | - `null` = **unknown**                                                                                   |
| `demographic`*         | Categorized using `CASE WHEN`:                                                                           |
|                        | - `C` = **Couples**                                                                                      |
|                        | - `F` = **Families**                                                                                     |
|                        | - `null` = **unknown**                                                                                   |
| `transactions`         | No changes required.                                                                                     |
| `sales`                | Cast to `BIGINT` for consistency in aggregations.                                                        |
| `avg_transaction`*     | Calculated as `sales / transactions`, rounded to 2 decimal places.                                       |

---

## SQL Code

The following SQL query was used to generate the **`clean_weekly_sales`** table:

```sql
SELECT
  TO_DATE(week_date, 'DD/MM/YY') AS week_date,
  EXTRACT(WEEK FROM TO_DATE(week_date, 'DD/MM/YY')) AS week_number,
  EXTRACT(MONTH FROM TO_DATE(week_date, 'DD/MM/YY')) AS month_number,
  EXTRACT(YEAR FROM TO_DATE(week_date, 'DD/MM/YY')) AS calendar_year,
  region,
  platform,
  segment,
  customer_type,
  CASE 
    WHEN RIGHT(segment, 1) = '1' THEN 'Young Adults'
    WHEN RIGHT(segment, 1) = '2' THEN 'Middle Aged'
    WHEN RIGHT(segment, 1) IN ('3', '4') THEN 'Retirees'
    ELSE 'unknown' END AS age_band,
  CASE 
    WHEN LEFT(segment, 1) = 'C' THEN 'Couples'
    WHEN LEFT(segment, 1) = 'F' THEN 'Families'
    ELSE 'unknown' END AS demographic,
  transactions,
  CAST(sales AS BIGINT) AS sales,
  ROUND(CAST(sales AS NUMERIC) / transactions, 2) AS avg_transaction
INTO clean_weekly_sales
FROM data_mart.weekly_sales;


## Output Sample

| **week_date** | **week_number** | **month_number** | **calendar_year** | **region** | **platform** | **segment** | **customer_type** | **age_band**   | **demographic** | **transactions** | **sales**   | **avg_transaction** |
|---------------|-----------------|------------------|--------------------|------------|--------------|-------------|-------------------|----------------|-----------------|------------------|-------------|---------------------|
| 8/31/2020     | 36              | 8                | 2020               | ASIA       | Retail       | C3          | New               | Retirees       | Couples         | 120631           | 3656163     | 30.31              |
| 8/31/2020     | 36              | 8                | 2020               | ASIA       | Retail       | F1          | New               | Young Adults   | Families        | 31574            | 996575      | 31.56              |
| 8/31/2020     | 36              | 8                | 2020               | USA        | Retail       | null        | Guest             | unknown        | unknown         | 529151           | 16509610    | 31.20              |
| 8/31/2020     | 36              | 8                | 2020               | EUROPE     | Retail       | C1          | New               | Young Adults   | Couples         | 4517             | 141942      | 31.42              |
| 8/31/2020     | 36              | 8                | 2020               | AFRICA     | Retail       | C2          | New               | Middle Aged    | Couples         | 58046            | 1758388     | 30.29              |
