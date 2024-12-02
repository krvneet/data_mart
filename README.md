# Case Study #5 - Data Mart  

📕 **Table of Contents**  
1. [Business Task](#business-task)  
2. [Entity Relationship Diagram](#entity-relationship-diagram)  
3. [Case Study Questions](#case-study-questions)  
4. [My Solution](#my-solution)  

---

## 🛠️ Business Task  
Data Mart is an online supermarket specializing in fresh produce. In June 2020, large-scale supply changes were introduced. All products now use sustainable packaging at every step, from the farm to the customer.  

Danny Ma needs help to analyze and quantify the impact of this change on sales performance.  

The key business questions to answer are:  
- **What was the quantifiable impact of the changes introduced in June 2020?**  
- **Which platform, region, segment, and customer types were most impacted by this change?**  
- **What can be done to minimize the impact of similar sustainability updates in the future?**

---

## 🔐 Entity Relationship Diagram  
*(Include an image or diagram of the schema here, if available.)*  

---

## ❓ Case Study Questions  

### A. Data Cleansing Steps  
[View my solution HERE](#)  

Perform the following operations to generate a new table `clean_weekly_sales`:  
- Convert `week_date` to a `DATE` format.  
- Add a `week_number` (e.g., Jan 1–7 = 1, Jan 8–14 = 2, etc.).  
- Add a `month_number` representing the calendar month.  
- Add a `calendar_year` (2018, 2019, 2020).  
- Add an `age_band` column based on `segment`:  
  - **1** → Young Adults  
  - **2** → Middle Aged  
  - **3, 4** → Retirees  
- Add a `demographic` column based on `segment`:  
  - **C** → Couples  
  - **F** → Families  
- Replace all `NULL` values in `segment`, `age_band`, and `demographic` with `unknown`.  
- Generate a new `avg_transaction` column (`sales` / `transactions` rounded to 2 decimals).  

---

### B. Data Exploration  
[View my solution HERE](#)  

Questions to explore:  
1. What day of the week is used for each `week_date`?  
2. Which `week_number` values are missing from the dataset?  
3. How many total transactions were there for each year?  
4. What are the total sales for each region for each month?  
5. What is the total count of transactions for each platform?  
6. What is the percentage of sales for Retail vs. Shopify for each month?  
7. What is the percentage of sales by demographic for each year?  
8. Which `age_band` and `demographic` contribute the most to Retail sales?  
9. Can the `avg_transaction` column be used to find the average transaction size? If not, how?  

---

### C. Before & After Analysis  
[View my solution HERE](#)  

Using `2020-06-15` as the baseline week:  
1. Compare the total sales for the 4 weeks before and after `2020-06-15`.  
2. Compare the entire 12 weeks before and after.  
3. How do the sales metrics for these periods compare with 2018 and 2019?  

---

### D. Bonus Question  
[View my solution HERE](#)  

Identify areas with the highest negative sales impact in 2020 for the 12-week period before and after `2020-06-15` across:  
- Region  
- Platform  
- Age Band  
- Demographic  
- Customer Type  

Provide recommendations and insights for Danny’s team to address these challenges.  

---

## 📝 My Solution  
- [SQL Code for Data Cleansing Steps](#)  
- [SQL Code for Data Exploration](#)  
- [SQL Code for Before & After Analysis](#)  
- [SQL Code for Bonus Question](#)  

---

### Author  
**Vineet Kumar**  

Feel free to reach out for collaboration or suggestions!  
