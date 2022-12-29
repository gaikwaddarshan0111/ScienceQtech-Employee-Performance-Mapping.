# ScienceQtech-Employee-Performance-Mapping.
ScienceQtech Employee Performance Mapping - Darshan Gaikwad
-- Task 1 - Create a database named employee, then import data_science_team.csv proj_table.csv and emp_record_table.csv into the employee database from the given resources

create database employee;

-- Task 2 - Create an ER diagram for the given employee database.

-- Task 3 - Write a query to fetch EMP_ID, FIRST_NAME, LAST_NAME, GENDER, and DEPARTMENT from the employee record table, and make a list of employees and details of their department.

select emp_id , first_name , last_name , gender , dept 
from 
emp_record_table;

-- Task 4 -Write a query to fetch EMP_ID, FIRST_NAME, LAST_NAME, GENDER, DEPARTMENT, and EMP_RATING if the EMP_RATING is: 
-- less than two
select emp_id , first_name , last_name , gender , dept , emp_rating
from emp_record_table
where emp_rating < 2;
-- greater than four 
select emp_id , first_name , last_name , gender , dept , emp_rating
from emp_record_table
where emp_rating > 4;
-- between two and four
select emp_id , first_name , last_name , gender , dept , emp_rating
from emp_record_table
where emp_rating between 2 and 4;


-- Task 5 Write a query to concatenate the FIRST_NAME and the LAST_NAME of employees in the Finance department from the employee table and then give the resultant column alias as NAME.

select concat(first_name," ",last_name) as name,dept
from emp_record_table
where dept = "FINANCE";

-- Task 6 Write a query to list only those employees who have someone reporting to them. 
-- Also, show the number of reporters (including the President).

SELECT employee. EMP_ID, CONCAT(employee.FIRST_NAME," ",employee.LAST_NAME) AS EMPLOYEE_NAME , manager.MANAGER_ID,
                         CONCAT(manager.FIRST_NAME," ",manager.LAST_NAME) AS MANAGER_NAME, manager.ROLE
                         FROM employee.emp_record_table employee
                         JOIN employee.emp_record_table manager
                         ON employee.MANAGER_ID = manager.EMP_ID;

-- Task 7 : Write a query to list down all the employees from the healthcare and finance departments using union.
--  Take data from the employee record table.

select emp_id,first_name , last_name , dept as department from employee.emp_record_table
where dept = "HEALTHCARE" union
select emp_id,first_name , last_name , dept as department from employee.emp_record_table
where dept = "FINANCE";

-- Task 8 Write a query to list down employee details such as EMP_ID, FIRST_NAME, LAST_NAME,
--  ROLE, DEPARTMENT, and EMP_RATING grouped by dept. Also include the respective employee rating
-- along with the max emp rating for the department.

select EMP_ID, FIRST_NAME, LAST_NAME,ROLE, DEPT,MAX(EMP_RATING) FROM employee.emp_record_table group by dept;

-- TASK 9 : Write a query to calculate the minimum and the maximum salary of the employees in each role. 
-- Take data from the employee record table.

select role , min(salary) as min_salary , max(salary) as max_salary from employee.emp_record_table group by role;


-- Task 10: Write a query to assign ranks to each employee based on their experience. Take data from the employee record table.

SELECT EMP_ID, FIRST_NAME, LAST_NAME, ROLE, DEPT, EXP, RANK() OVER (ORDER BY EXP DESC) AS RANKING FROM employee.emp_record_table;

-- Task 11: Write a query to create a view that displays employees in various countries whose salary is more than six thousand.
--  Take data from the employee record table.

SELECT*FROM employee.emp_record_table;
CREATE VIEW EMPLOYEE_SALARY_VIEW AS SELECT EMP_ID, FIRST_NAME, LAST_NAME, COUNTRY, SALARY FROM employee.emp_record_table WHERE SALARY > 6000;
SELECT*FROM EMPLOYEE_SALARY_VIEW;

-- Task 12 : Write a nested query to find employees with experience of more than ten years. Take data from the employee record table.

SELECT e.EMP_ID, e.FIRST_NAME, e.LAST_NAME, e.EXP, (SELECT COUNT(DISTINCT p.EMP_ID) FROM employee.emp_record_table p) AS EXP1 FROM employee.emp_record_table e WHERE e.EXP>10;

-- Task 13 : Write a query to create a stored procedure to retrieve the details of the employees whose experience is more than three years. Take data from the employee record table
Delimiter $$
CREATE PROCEDURE Get_Employee_Exp()
BEGIN
SELECT*FROM employee.emp_record_table WHERE EXP > 3;
END $$
CALL Get_Employee_Exp;

-- Task 14:
-- Write a query using stored functions in the project table to check whether the job profile assigned to each employee in 
-- the data science team matches the organization’s set standard.
-- The standard being:
-- For an employee with experience less than or equal to 2 years assign 'JUNIOR DATA SCIENTIST',
-- For an employee with the experience of 2 to 5 years assign 'ASSOCIATE DATA SCIENTIST',
-- For an employee with the experience of 5 to 10 years assign 'SENIOR DATA SCIENTIST',
-- For an employee with the experience of 10 to 12 years assign 'LEAD DATA SCIENTIST',
-- For an employee with the experience of 12 to 16 years assign 'MANAGER'.

Delimiter $$
CREATE FUNCTION EMPLOYEE_JOB_PROFILE(EXP int)
RETURNS VARCHAR(100) DETERMINISTIC
BEGIN
DECLARE EMPLOYEE_JOB_PROFILE VARCHAR(100);
if EXP <= 2 THEN SET EMPLOYEE_JOB_PROFILE = 'JUNIOR DATA SCIENTIST';
elseif EXP BETWEEN 2 AND 5 THEN SET EMPLOYEE_JOB_PROFILE = 'ASSOCIATE DATA SCIENTIST';
elseif EXP BETWEEN 5 AND 10 THEN SET EMPLOYEE_JOB_PROFILE = 'SENIOR DATA SCIENTIST';
elseif EXP BETWEEN 10 AND 12 THEN SET EMPLOYEE_JOB_PROFILE = 'LEAD DATA SCIENTIST';
elseif EXP BETWEEN 12 AND 16 THEN SET EMPLOYEE_JOB_PROFILE = 'MANAGER';
END IF;
RETURN (EMPLOYEE_JOB_PROFILE);
END $$
SELECT EMP_ID , FIRST_NAME , EXP , EMPLOYEE_JOB_PROFILE(EXP) FROM employee.emp_record_table;

-- Task 15 :Create an index to improve the cost and performance of the query to find the employee
--  whose FIRST_NAME is ‘Eric’ in the employee table after checking the execution plan.

create index IDX_FIRST_NAME ON employee.emp_record_table(FIRST_NAME);
explain select EMP_ID , FIRST_NAME , LAST_NAME FROM employee.emp_record_table where FIRST_NAME = "ERIC";

-- Task 16 : Write a query to calculate the bonus for all the employees, based on their ratings and salaries
-- (Use the formula: 5% of salary * employee rating).

select EMP_ID , FIRST_NAME , LAST_NAME , ROLE, DEPT, EXP, SALARY, EMP_RATING, (SALARY * 5/100)*(EMP_RATING) AS BONUS FROM employee.emp_record_table;

-- Task17: Write a query to calculate the average salary distribution based on the continent and country. 
-- Take data from the employee record table.
SELECT EMP_ID, FIRST_NAME, LAST_NAME, COUNTRY, CONTINENT, AVG(SALARY) AS AVERAGE_SALARY FROM employee.emp_record_table GROUP BY CONTINENT, COUNTRY;
