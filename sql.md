
# Financial Data Analysis

## 1. Simple Select Queries

**1.1 List all loans with an interest rate above 15%.**

```sql
SELECT * 
FROM Financial_analysis_loandata
WHERE int_rate >0.15;
```
**1.2 Find all customers who have 10 years and above of employment and own a home.**
```sql
SELECT *
FROM Financial_analysis_customerdata
WHERE emp_length_num >= 10
  AND home_ownership IN ('OWN', 'MORTGAGE');
```
***

## 2. Joins

**2.1 Join the customerdata and loandata tables to find customers who have loans with a term of 60 months.**
#### Steps
- Created a CTE named 'Financial_analysis_loandata_cleaned' to include new concatenated column 'loan_id'.
- Joined the CTE with Financial_analysis_customerdata on loan_id and filter results where term_months is 60.
  
```sql
WITH Financial_analysis_loandata_cleaned AS(
	SELECT CONCAT(loan_info_id,'-',loan_id_number) AS loan_id, loan_amnt, funded_amnt, term_months, int_rate, installment, annual_inc, monthly_inc, minc_to_minst, verification_status, issue_date, loan_status, dti, delinq_2yrs
	FROM Financial_analysis_loandata
	)

SELECT *
FROM Financial_analysis_customerdata C JOIN Financial_analysis_loandata_cleaned L
		ON C.loan_id=L.loan_id
WHERE term_months=60;
```
**2.2 Find the total loan amount for each customer by joining the customerdata and loandata tables.**
***

## 3. Aggregate Functions

**3.1 Calculate the average interest rate for loans with a status of "Current".**
**3.2 Find the maximum loan amount for each loan grade.**
***

## 4. Filtering and Grouping

**4.1 List the total number of loans by state.**
**4.2 Find the average employment length for customers with loans greater than $20,000.**
***

## 5. Subqueries

**5.1 Find the customers who have a debt-to-income ratio higher than the average across all loans.**
**5.2 List all loans where the funded amount is less than the loan amount.**
***

## 6. Date and String Functions

**6.1 Extract the year from the issue date and count the number of loans issued each year.**
**6.2 Find all customers whose job title starts with "Finance".**
