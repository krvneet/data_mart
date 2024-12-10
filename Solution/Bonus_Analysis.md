## Bonus Question

### Business Areas with Highest Negative Impact on Sales Metrics Performance in 2020

This analysis inspects the impact of an important event, focusing on sales metrics before and after 12 weeks. 
The baseline week for this analysis is `2020-06-15`, where sustainable packaging changes were implemented.

Firstly, we determine the `week_number` corresponding to '2020-06-15' to use it as a filter in our analysis.

**Code**:
```sql
SELECT DISTINCT week_number
FROM clean_weekly_sales
WHERE week_date = '2020-06-15' 
  AND calendar_year = '2020';
```

**Output**:
```
week_number
25
```

---

### Sales Changes by Regions

**Code**:
```sql
WITH sale_changes AS (
 SELECT
    region, 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 GROUP BY region
)
SELECT
   region,
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output**:
| Region         | Before Change | After Change | % Change |
|----------------|---------------|--------------|----------|
| South America  | 611056923     | 608981392    | -0.34    |
| Asia           | 4613242689    | 4551927271   | -1.33    |
| Oceania        | 6698586333    | 6640244793   | -0.87    |
| Europe         | 328141414     | 344420043    | 4.96     |
| Canada         | 1244662705    | 1234025206   | -0.85    |
| Africa         | 4942976910    | 4997516159   | 1.1      |
| USA            | 1967554887    | 1960297502   | -0.37    |

---

### Sales Changes by Platform

**Code**:
```sql
WITH sale_changes AS (
 SELECT
    platform, 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 GROUP BY platform
)
SELECT
   platform,
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output**:
| Platform | Before Change  | After Change   | % Change |
|----------|----------------|----------------|----------|
| Shopify  | 520181589      | 568836201      | 9.35     |
| Retail   | 19886040272    | 19768576165    | -0.59    |

---

### Sales Changes by Age Band

**Code**:
```sql
WITH sale_changes AS (
 SELECT
    age_band, 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 GROUP BY age_band
)
SELECT
   age_band,
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output**:
| Age Band      | Before Change  | After Change   | % Change |
|---------------|----------------|----------------|----------|
| Young Adults  | 2290835366     | 2285973456     | -0.21    |
| Retirees      | 6646865322     | 6634706880     | -0.18    |
| Middle Aged   | 3276892347     | 3269748622     | -0.22    |
| Unknown       | 8191628826     | 8146983408     | -0.55    |

---

### Sales Changes by Demographic

**Code**:
```sql
WITH sale_changes AS (
 SELECT
   demographic, 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 GROUP BY demographic
)
SELECT
   demographic,
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output**:
| Demographic    | Before Change  | After Change   | % Change |
|----------------|----------------|----------------|----------|
| Young Adults   | 2290835366     | 2285973456     | -0.21    |
| Retirees       | 6646865322     | 6634706880     | -0.18    |
| Middle Aged    | 3276892347     | 3269748622     | -0.22    |
| Unknown        | 8191628826     | 8146983408     | -0.55    |

---

### Sales Changes by Customer Type

**Code**:
```sql
WITH sale_changes AS (
 SELECT
   customer_type, 
    SUM(CASE WHEN week_number BETWEEN 13 AND 24 THEN sales END) AS before_change,
    SUM(CASE WHEN week_number BETWEEN 25 AND 36 THEN sales END) AS after_change
 FROM clean_weekly_sales    
 GROUP BY customer_type)
SELECT
    customer_type,
   before_change, after_change,
   ROUND(((after_change - before_change) / before_change) * 100, 2) AS pcnt_change
FROM sale_changes;
```

**Output**:
| Customer Type | Before Change  | After Change   | % Change |
|---------------|----------------|----------------|----------|
| Guest         | 7630353739     | 7595150744     | -0.46    |
| New           | 2606990480     | 2624894383     | 0.69     |
| Existing      | 10168877642    | 10117367239    | -0.51    |

---

## Conclusion

The analysis identifies the following key insights:

- **Regions**: Asia faced the largest negative change in sales at -1.33%, while Europe experienced a positive change of 4.96%.
- **Platforms**: Shopify showed a notable growth of 9.35%, whereas Retail had a slight decline of -0.59%.
- **Age Bands**: All age groups exhibited minor negative changes, with "Unknown" age band showing the highest decline at -0.55%.
- **Demographics**: Similar patterns as age bands were observed, with negligible changes across demographics.
- **Customer Types**: "New" customers demonstrated a slight increase of 0.69%, while "Guest" and "Existing" customers faced declines.

These findings can help prioritize efforts to address areas with negative performance and replicate strategies that yielded positive outcomes.
