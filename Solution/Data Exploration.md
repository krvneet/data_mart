
# Data Exploration

## 1. What day of the week is used for each week_date value?

**Code:**  
```sql
SELECT DISTINCT(TO_CHAR(week_date, 'day')) AS week_day 
FROM clean_weekly_sales;
```

**Output:**  
```
week_day
monday   
```

**Conclusion:**  
Monday is used for the week_date value.

---

## 2. What range of week numbers are missing from the dataset?

**Code:**  
```sql
WITH allWeeks AS (
 SELECT GENERATE_SERIES(1, 52) AS pos)
SELECT DISTINCT a.pos AS missing_weeks
FROM allweeks AS a 
LEFT JOIN clean_weekly_sales AS c 
ON a.pos = c.week_number
WHERE c.week_number IS NULL;
```

**Output:**  
```
missing_weeks
1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52
```

**Conclusion:**  
The dataset is missing a total of 28 week_number records.

---

## 3. How many total transactions were there for each year in the dataset?

**Code:**  
```sql
SELECT calendar_year AS year, SUM(transactions) AS total_transactions 
FROM clean_weekly_sales
GROUP BY calendar_year;
```

**Output:**  
```
year	total_transactions
2019	365639285
2018	346406460
2020	375813651
```

---

## 4. What is the total sales for each region for each month?

**Code:**  
```sql
SELECT region, month_number, SUM(sales) as total_sales
FROM clean_weekly_sales
GROUP BY region, month_number
ORDER BY region, month_number;
```

**Output (March):**  
```
region	      month_number	 total_sales
AFRICA	        3	          567767480
ASIA	          3	          529770793
CANADA	        3	          144634329
EUROPE 	       3	          35337093
OCEANIA	       3	          783282888
SOUTH AMERICA	 3	          71023109
USA	           3	          225353043
```

---

## 5. What is the total count of transactions for each platform?

**Code:**  
```sql
SELECT platform , COUNT(transactions) AS total_count_transactions
FROM clean_weekly_sales
GROUP BY platform;
```

**Output:**  
```
platform	total_count_transactions
Shopify	 8549
Retail	  8568
```

---

## 6. What is the percentage of sales for Retail vs Shopify for each month?

**Code:**  
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
       ROUND((MAX(CASE WHEN platform = 'Shopify' THEN monthly_sales END)
        / SUM(monthly_sales))*100,2) AS shopify_prc,
       ROUND((MAX(CASE WHEN platform = 'Retail' THEN monthly_sales END)
        / SUM(monthly_sales))*100,2) AS Retail_prc
FROM sale_cte
GROUP BY calendar_year, month_number
ORDER BY calendar_year, month_number;
```

**Output (2018):**  
```
calendar_year	month_number	 shopify	retail
2018	       3	       2.08	       97.92
2018	       4	       2.07	       97.93
2018	       5	       2.27	       97.73
2018	       6	       2.24	       97.76
2018	       7	       2.25	       97.75
2018	       8	       2.29	       97.71
2018	       9	       2.32	       97.68
```

---

## 7. What is the percentage of sales by demographic for each year in the dataset?

**Code:**  
```sql
WITH year_sales AS (
 SELECT calendar_year, 
 demographic,
 SUM(sales) AS sum_sales
 FROM clean_weekly_sales
 GROUP BY calendar_year, demographic
)
SELECT calendar_year, 
       ROUND((MAX(CASE WHEN demographic = 'Couples' THEN sum_sales END)
       / SUM(sum_sales))*100, 2) AS couples,
       ROUND((MAX(CASE WHEN demographic = 'Families' THEN sum_sales END)
       / SUM(sum_sales))*100, 2) AS families,
       ROUND((MAX(CASE WHEN demographic = 'unknown' THEN sum_sales END)
       / SUM(sum_sales))*100, 2) AS unknown
FROM year_sales
GROUP BY calendar_year;
```

**Output:**  
```
calendar_year	couples	   families	     unknown
2018	         26.38	       31.99	       41.63
2019	         27.28	       32.47	       40.25
2020	         28.72	       32.73	       38.55
```

---

## 8. Which age_band and demographic values contribute the most to Retail sales?

**Code:**  
```sql
SELECT age_band , demographic, SUM(sales) , 
       ROUND((SUM(sales)/ ( SELECT SUM(sales) 
                    FROM clean_weekly_sales 
                    WHERE platform = 'Retail'))*100,2) AS contributor
FROM clean_weekly_sales
WHERE platform = 'Retail'
GROUP BY age_band , demographic
ORDER BY contributor DESC;
```

**Output:**  
```
age_band	     demographic	 sum	        contributor
unknown	        unknown	  16067285533	 40.52
Retirees       	Families 	6634686916  	16.73
Retirees       	Couples	  6370580014  	16.07
Middle Aged	    Families 	4354091554  	10.98
Young Adults	   Couples	  2602922797  	6.56
Middle Aged	    Couples	  1854160330  	4.68
Young Adults   	Families 	1770889293  	4.47
```

---

## 9. Can we use the avg_transaction column to find the average transaction size for each year for Retail vs Shopify? If not - how would you calculate it instead?

**Code:**  
```sql
SELECT calendar_year, platform,
       ROUND(AVG(avg_transaction), 0) AS avg_transaction_row,
       ROUND(SUM(sales)/SUM(transactions), 0) AS avg_grp_transactions
FROM clean_weekly_sales
GROUP BY calendar_year, platform
ORDER BY calendar_year, platform;
```

**Output:**  
```
calendar_year	platform 	avg_transaction_row	 avg_grp_transactions
2018	        Retail	        43	                 37
2018	        Shopify	        188	                192
2019	        Retail     	42	                 37
2019	        Shopify    	178	                183
2020	        Retail     	41	                 37
2020	        Shopify    	175	                179
```
