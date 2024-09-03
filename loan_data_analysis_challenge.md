## 1. Customer and Loan Data Analysis
**Question: Find the top 5 states with the highest average loan amount among customers with more than 2 years of employment. For each state, include the count of loans and the average interest rate.**
```sql
WITH loandata_cleaned AS(
   -- Combine loan_info_id and loan_id_number to create a unique loan_id 
   -- that matches the loan_id in the customerdata table for joining
	SELECT CONCAT(loan_info_id,'-',loan_id_number) AS loan_id, loan_amnt, funded_amnt, term_months, int_rate, installment, annual_inc, monthly_inc, minc_to_minst, verification_status, issue_date, loan_status, dti, delinq_2yrs
	FROM Financial_analysis_loandata
	)

SELECT C.state, 
	   AVG(L.loan_amnt) AS Avg_loan_amnt, 
	   COUNT(C.loan_id) AS Count_of_loans, 
	   AVG(L.int_rate) AS Avg_int_rate
FROM Financial_analysis_customerdata C
JOIN loandata_cleaned L
	ON C.loan_id=L.loan_id
WHERE emp_length_num>2
GROUP BY C.state
ORDER BY 2 DESC
OFFSET 0 ROWS FETCH NEXT 5 ROWS ONLY -- LIMIT clause is not supported by SQL Server. Hence, I'm using this clause to achive the same result
; 
```
## Query Result:
![image](https://github.com/user-attachments/assets/d0393b53-e50c-4091-a942-fd380dfdc90d)

***
## 2. Monthly Income vs. Loan Installments
**Question: For each loan term (e.g., 36 months, 60 months), calculate the percentage of customers where the monthly income is less than 2 times the monthly installment amount.**
```sql
WITH loandata_cleaned AS(
   -- Combine loan_info_id and loan_id_number to create a unique loan_id 
   -- that matches the loan_id in the customerdata table for joining
	SELECT CONCAT(loan_info_id,'-',loan_id_number) AS loan_id, loan_amnt, funded_amnt, term_months, int_rate, installment, annual_inc, monthly_inc, minc_to_minst, verification_status, issue_date, loan_status, dti, delinq_2yrs
	FROM Financial_analysis_loandata
	)

SELECT L.term_months, COUNT(CASE WHEN L.monthly_inc < (2*L.installment) THEN 1 ELSE NULL END)*100.0/ COUNT(*) AS percentage_cust
FROM Financial_analysis_customerdata C
JOIN loandata_cleaned L
	ON C.loan_id=L.loan_id
GROUP BY L.term_months
```
## Query Result:
![image](https://github.com/user-attachments/assets/16b96eb6-98da-40dd-b8b6-baa61a1a0701)
