
# Loan Data Analysis

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
```sql
WITH Financial_analysis_loandata_cleaned AS(
	SELECT CONCAT(loan_info_id,'-',loan_id_number) AS loan_id, loan_amnt, funded_amnt, term_months, int_rate, installment, annual_inc, monthly_inc, minc_to_minst, verification_status, issue_date, loan_status, dti, delinq_2yrs
	FROM Financial_analysis_loandata
	)

SELECT c.loan_id, c.emp_title, L.loan_amnt
FROM Financial_analysis_customerdata C JOIN Financial_analysis_loandata_cleaned L
		ON C.loan_id=L.loan_id;
```
***

## 3. Aggregate Functions

**3.1 Calculate the average interest rate for loans with a status of "Current".**
```sql
SELECT AVG(int_rate) AS average_interest_rate
FROM Financial_analysis_loandata
WHERE loan_status='current';
```
**3.2 Find the maximum loan amount for each loan grade.**
```sql
WITH Financial_analysis_loandata_cleaned AS(
	SELECT CONCAT(loan_info_id,'-',loan_id_number) AS loan_id, loan_amnt, funded_amnt, term_months, int_rate, installment, annual_inc, monthly_inc, minc_to_minst, verification_status, issue_date, loan_status, dti, delinq_2yrs
	FROM Financial_analysis_loandata
	)

SELECT c.grade, max(l.loan_amnt) AS max_loan_amount
FROM Financial_analysis_customerdata C
JOIN Financial_analysis_loandata_cleaned L
	ON C.loan_id=L.loan_id
GROUP BY C.grade
ORDER BY c.grade;
```
***

## 4. Filtering and Grouping

**4.1 List the total number of loans by state.**
- I can count the rows in the customer table to determine the total number of loans because the loan_id column is unique, meaning each row represents a distinct loan.
```sql
SELECT state, COUNT(*) AS total_loans
FROM Financial_analysis_customerdata
GROUP BY state
ORDER BY 2 DESC;
```


**4.2 Find the average employment length for customers with loans greater than $20,000.**
***
```sql
WITH Financial_analysis_loandata_cleaned AS(
	SELECT CONCAT(loan_info_id,'-',loan_id_number) AS loan_id, loan_amnt, funded_amnt, term_months, int_rate, installment, annual_inc, monthly_inc, minc_to_minst, verification_status, issue_date, loan_status, dti, delinq_2yrs
	FROM Financial_analysis_loandata
	)

SELECT AVG(C.emp_length_num) AS avg_employment_length
FROM Financial_analysis_customerdata C JOIN Financial_analysis_loandata_cleaned L
		ON C.loan_id=L.loan_id
WHERE L.loan_amnt>20000;
```
## 5. Subqueries

**5.1 Find the customers who have a debt-to-income ratio higher than the average across all loans.**
```sql
WITH Financial_analysis_loandata_cleaned AS (
    SELECT CONCAT(loan_info_id, '-', loan_id_number) AS loan_id, 
           loan_amnt, funded_amnt, term_months, int_rate, installment, 
           annual_inc, monthly_inc, minc_to_minst, verification_status, 
           issue_date, loan_status, dti, delinq_2yrs
    FROM Financial_analysis_loandata
)

SELECT C.loan_id, C.emp_title, 
       L.dti AS debt_to_inc_ratio
FROM Financial_analysis_customerdata C
JOIN Financial_analysis_loandata_cleaned L ON C.loan_id = L.loan_id
WHERE L.dti > (
    SELECT AVG(dti)
    FROM Financial_analysis_loandata
);
```
***

## 6. Date and String Functions

**6.1 Extract the year from the issue date and count the number of loans issued each year.**
```sql
WITH loanyear AS (
	SELECT YEAR(issue_date) AS Year
	FROM Financial_analysis_loandata
)

SELECT Year, count(*) AS total_num_of_loans
FROM loanyear
GROUP BY YEAR
ORDER BY YEAR;
```
**6.2 Find all customers whose job title starts with "Finance".**
```sql
SELECT *
FROM Financial_analysis_customerdata
WHERE emp_title LIKE 'Finance%';
```
