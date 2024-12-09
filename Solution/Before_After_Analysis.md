
# Before & After Analysis

This analysis inspects the impact of an important event, focusing on sales metrics before and after a specific point in time. The baseline week for this analysis is `2020-06-15`, where sustainable packaging changes were implemented. 

## Questions and Insights

### 1. Total Sales for 4 Weeks Before and After `2020-06-15` and What is the growth or reduction rate in actual values and percentage of sales
 
 firstly, we determine the week_number corresponding to '2020-06-15' to use it as a filter in our analysis.

 ***code:***
 ```sql
 SELECT DISTINCT week_number
FROM clean_weekly_sales
WHERE week_date = '2020-06-15' 
  AND calendar_year = '2020';
```

| week_number |
|-------------|
|     25      |
       

The week_number is 25. I created CTEs: 

sale_changes CTE: Utilize a CASE statement to capture the sales for 4 weeks before and after 2020-06-15 and then calculate the total sales for the specified period.

We calculated the total sales for 4 weeks before and after the change and the growth or reduction rate.

**Code:**
```sql
WITH sale_changes AS (
 SELECT 
    SUM(CASE WHEN week_number BETWEEN weekNum-4 AND weekNum-1 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN weekNum AND weekNum+3 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 WHERE calendar_year = '2020'
)
SELECT
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output:**

| Before Change | After Change | Percentage Change |
|----------------|--------------|-------------------|
| 2,345,878,357  | 2,318,994,169| -1.15%            |

**Insight:**  
Sales decreased by $26,884,188, representing a 1.15% decline after introducing the new sustainable packaging.

---

### 2. Total Sales for 12 Weeks Before and After `2020-06-15`
Using the same approach, we analyzed sales over 12 weeks.

**Code:**
```sql
WITH sale_changes AS (
 SELECT 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 WHERE calendar_year = '2020'
)
SELECT
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output:**

| Before Change | After Change | Percentage Change |
|----------------|--------------|-------------------|
| 7,126,273,147  | 6,973,947,753| -2.14%            |

**Insight:**  
Sales declined further, with a 2.14% reduction over the 12-week analysis.

---

### 3. Comparison with Previous Years (2018 & 2019)
We compared sales metrics for 4 and 12 weeks before and after `2020-06-15` with those of 2018 and 2019.

#### Part 1: 4-Week Comparison

**Code:**
```sql
WITH sale_changes AS (
 SELECT 
    SUM(CASE WHEN week_number BETWEEN 21 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 28 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 GROUP BY calendar_year
)
SELECT
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```



**Output:**

| Year | Before Change | After Change | Percentage Change |
|------|---------------|--------------|-------------------|
| 2018 | 6,883,386,397 | 6,862,646,103| -0.30%            |
| 2019 | 6,396,562,317 | 6,500,818,510| +1.63%            |
| 2020 | 7,126,273,147 | 6,973,947,753| -2.14%            |

#### Part 2: 12-Week Comparison

**Code:**
```sql
WITH sale_changes AS (
 SELECT 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 GROUP BY calendar_year
)
SELECT
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output:**

| Year | Before Change | After Change | Percentage Change |
|------|---------------|--------------|-------------------|
| 2018 | 2,249,989,796 | 2,252,326,390| +0.10%            |
| 2019 | 2,125,140,809 | 2,129,242,914| +0.19%            |
| 2020 | 2,345,878,357 | 2,318,994,169| -1.15%            |

**Insight:**  
The percentage changes reveal notable variations across years, with a sharp decline in 2020 compared to slight increases in 2018 and 2019.

---

### Conclusion
- The sales decline post-packaging changes in 2020 suggests potential customer challenges in recognizing products.
- Further investigation and strategy adjustments may be required to address this issue.

**Note:**  
This analysis provides a foundation for deeper insights into the impact of packaging change on sales.

---

