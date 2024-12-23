
# Data Exploration

## 1. What day of the week is used for each `week_date` value?

**Code**:
```sql
SELECT DISTINCT(TO_CHAR(week_date, 'day')) AS week_day 
FROM clean_weekly_sales;
```

**Output**:
| week_day |
|----------|
| Monday   |

Monday is used for the `week_date` value.

---

## 2. What range of week numbers are missing from the dataset?

**Code**:
```sql
WITH allWeeks AS (
  SELECT GENERATE_SERIES(1, 52) AS pos
)
SELECT DISTINCT a.pos AS missing_weeks
FROM allWeeks AS a 
LEFT JOIN clean_weekly_sales AS c 
ON a.pos = c.week_number
WHERE c.week_number IS NULL;
```

**Output**:
| missing_weeks |
|---------------|
| 1             |
| 2             |
| ...           |
| 51            |
| 52            |

The dataset is missing a total of 28 `week_number` records.

---

## 3. How many total transactions were there for each year in the dataset?

**Code**:
```sql
SELECT calendar_year AS year, SUM(transactions) AS total_transactions 
FROM clean_weekly_sales
GROUP BY calendar_year;
```

**Output**:
| year | total_transactions |
|------|---------------------|
| 2019 | 365639285          |
| 2018 | 346406460          |
| 2020 | 375813651          |

---

## 4. What is the total sales for each region for each month?

**Code**:
```sql
SELECT region, month_number, SUM(sales) AS total_sales
FROM clean_weekly_sales
GROUP BY region, month_number
ORDER BY region, month_number;
```

**Output (March only)**:
| region         | month_number | total_sales |
|----------------|--------------|-------------|
| AFRICA         | 3            | 567767480   |
| ASIA           | 3            | 529770793   |
| ...            | ...          | ...         |
| USA            | 3            | 225353043   |


## 5. What is the total count of transactions for each platform?

**Code**:
```sql
SELECT platform, COUNT(transactions) AS total_count_transactions
FROM clean_weekly_sales
GROUP BY platform;
```

**Output**:
| platform | total_count_transactions |
|----------|---------------------------|
| Shopify  | 8549                      |
| Retail   | 8568                      |


## 6. What is the percentage of sales for Retail vs Shopify for each month?

**Code**:
```sql
WITH sale_cte AS (
  SELECT calendar_year, 
         month_number,
         platform,
         SUM(sales) AS monthly_sales
  FROM clean_weekly_sales
  GROUP BY calendar_year, month_number, platform
)
SELECT calendar_year, month_number,
       ROUND((MAX(CASE WHEN platform = 'Shopify' THEN monthly_sales END) / SUM(monthly_sales))*100, 2) AS shopify_prc,
       ROUND((MAX(CASE WHEN platform = 'Retail' THEN monthly_sales END) / SUM(monthly_sales))*100, 2) AS retail_prc
FROM sale_cte
GROUP BY calendar_year, month_number
ORDER BY calendar_year, month_number;
```

**Output**:
| calendar_year | month_number | shopify | retail |
|---------------|--------------|---------|--------|
| 2018          | 3            | 2.08    | 97.92  |
| ...           | ...          | ...     | ...    |



## 7. What is the percentage of sales by demographic for each year in the dataset?

**Code**:
```sql
WITH year_sales AS (
  SELECT calendar_year, 
         demographic,
         SUM(sales) AS sum_sales
  FROM clean_weekly_sales
  GROUP BY calendar_year, demographic
)
SELECT calendar_year, 
       ROUND((MAX(CASE WHEN demographic = 'Couples' THEN sum_sales END) / SUM(sum_sales))*100, 2) AS couples,
       ROUND((MAX(CASE WHEN demographic = 'Families' THEN sum_sales END) / SUM(sum_sales))*100, 2) AS families,
       ROUND((MAX(CASE WHEN demographic = 'unknown' THEN sum_sales END) / SUM(sum_sales))*100, 2) AS unknown
FROM year_sales
GROUP BY calendar_year;
```

**Output**:
| calendar_year | couples | families | unknown |
|---------------|---------|----------|---------|
| 2018          | 26.38   | 31.99    | 41.63   |
| 2019          | 27.28   | 32.47    | 40.25   |
| 2020          | 28.72   | 32.73    | 38.55   |


## 8. Which `age_band` and `demographic` values contribute the most to Retail sales?

**Code**:
```sql
SELECT age_band, demographic, SUM(sales) AS sum_sales, 
       ROUND((SUM(sales) / (SELECT SUM(sales) FROM clean_weekly_sales WHERE platform = 'Retail'))*100, 2) AS contributor
FROM clean_weekly_sales
WHERE platform = 'Retail'
GROUP BY age_band, demographic
ORDER BY contributor DESC;
```

**Output**:
| age_band    | demographic | sum_sales   | contributor |
|-------------|-------------|-------------|-------------|
| unknown     | unknown     | 16067285533 | 40.52       |
| Retirees    | Families    | 6634686916  | 16.73       |
| ...         | ...         | ...         | ...         |


## 9. Can we use the `avg_transaction` column to find the average transaction size for each year for Retail vs Shopify? If not, how would you calculate it instead?

**Code**:
```sql
SELECT calendar_year, platform,
       ROUND(AVG(avg_transaction), 0) AS avg_transaction_row,
       ROUND(SUM(sales)/SUM(transactions), 0) AS avg_grp_transactions
FROM clean_weekly_sales
GROUP BY calendar_year, platform
ORDER BY calendar_year, platform;
```

**Output**:
| calendar_year | platform | avg_transaction_row | avg_grp_transactions |
|---------------|----------|---------------------|-----------------------|
| 2018          | Retail   | 43                  | 37                    |
| 2018          | Shopify  | 188                 | 192                   |
| ...           | ...      | ...                 | ...                   |


**Note**: Replace `...` with actual results where applicable to complete the dataset representation.
