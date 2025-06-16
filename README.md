# top_sql_questions_for_interviews

# Complete SQL Interview Guide with Examples

## Sample Tables for Examples

```sql
-- Employees Table
CREATE TABLE employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(50),
    salary DECIMAL(10,2),
    hire_date DATE,
    manager_id INT
);

-- Departments Table
CREATE TABLE departments (
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(50),
    location VARCHAR(100)
);

-- Orders Table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    status VARCHAR(20)
);

-- Order_Items Table
CREATE TABLE order_items (
    item_id INT PRIMARY KEY,
    order_id INT,
    product_name VARCHAR(100),
    quantity INT,
    price DECIMAL(10,2)
);

-- Sample Data
INSERT INTO employees VALUES
(1, 'John Smith', 'IT', 75000, '2020-01-15', NULL),
(2, 'Sarah Johnson', 'HR', 65000, '2019-03-20', 1),
(3, 'Mike Davis', 'IT', 80000, '2021-06-10', 1),
(4, 'Emily Brown', 'Finance', 70000, '2020-11-05', NULL),
(5, 'David Wilson', 'IT', 60000, '2022-02-28', 3);
```

---

## 1. Basic SQL Commands (CRUD)

### **Syntax & Examples**

#### SELECT
```sql
-- Basic SELECT
SELECT column1, column2 FROM table_name;

-- SELECT with WHERE
SELECT * FROM employees WHERE salary > 70000;

-- SELECT with multiple conditions
SELECT name, salary FROM employees 
WHERE department = 'IT' AND salary > 65000;
```

#### INSERT
```sql
-- Single row insert
INSERT INTO employees (emp_id, name, department, salary, hire_date)
VALUES (6, 'Alice Green', 'Marketing', 55000, '2023-01-10');

-- Multiple rows insert
INSERT INTO employees VALUES
(7, 'Bob Smith', 'Sales', 50000, '2023-02-15', NULL),
(8, 'Carol White', 'Marketing', 52000, '2023-03-01', NULL);
```

#### UPDATE
```sql
-- Update single column
UPDATE employees SET salary = 82000 WHERE emp_id = 3;

-- Update multiple columns
UPDATE employees 
SET salary = salary * 1.1, department = 'Senior IT'
WHERE department = 'IT' AND salary > 75000;
```

#### DELETE
```sql
-- Delete specific rows
DELETE FROM employees WHERE emp_id = 8;

-- Delete with conditions
DELETE FROM employees WHERE hire_date < '2020-01-01';
```

### **Real-Time Scenarios & Questions**

#### **Easy Level**
**Scenario**: HR needs to find all employees in the IT department.
```sql
-- Question: Write a query to show all IT employees
SELECT * FROM employees WHERE department = 'IT';
```

#### **Medium Level**
**Scenario**: Update salaries for all employees hired before 2021 by 5%.
```sql
-- Question: Write an update query for salary increment
UPDATE employees 
SET salary = salary * 1.05 
WHERE hire_date < '2021-01-01';
```

#### **Hard Level**
**Scenario**: Insert new employees but avoid duplicates based on name and department.
```sql
-- Question: Write an insert query that prevents duplicates
INSERT INTO employees (emp_id, name, department, salary, hire_date)
SELECT 9, 'New Employee', 'IT', 60000, '2023-04-01'
WHERE NOT EXISTS (
    SELECT 1 FROM employees 
    WHERE name = 'New Employee' AND department = 'IT'
);
```

---

## 2. Joins

### **Syntax & Types**

#### INNER JOIN
```sql
SELECT e.name, d.dept_name
FROM employees e
INNER JOIN departments d ON e.department = d.dept_name;
```

#### LEFT JOIN
```sql
SELECT e.name, d.dept_name, d.location
FROM employees e
LEFT JOIN departments d ON e.department = d.dept_name;
```

#### RIGHT JOIN
```sql
SELECT e.name, d.dept_name
FROM employees e
RIGHT JOIN departments d ON e.department = d.dept_name;
```

#### FULL OUTER JOIN
```sql
SELECT e.name, d.dept_name
FROM employees e
FULL OUTER JOIN departments d ON e.department = d.dept_name;
```

#### CROSS JOIN
```sql
SELECT e.name, d.dept_name
FROM employees e
CROSS JOIN departments d;
```

#### SELF JOIN
```sql
-- Find employees and their managers
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
LEFT JOIN employees e2 ON e1.manager_id = e2.emp_id;
```

### **Real-Time Scenarios & Questions**

#### **Easy Level**
**Scenario**: Get employee names with their department locations.
```sql
-- Question: Show employees with department info
SELECT e.name, e.salary, d.location
FROM employees e
INNER JOIN departments d ON e.department = d.dept_name;
```

#### **Medium Level**
**Scenario**: Find all departments and count of employees in each.
```sql
-- Question: Department-wise employee count including departments with no employees
SELECT d.dept_name, COUNT(e.emp_id) as employee_count
FROM departments d
LEFT JOIN employees e ON d.dept_name = e.department
GROUP BY d.dept_name;
```

#### **Hard Level**
**Scenario**: Find employees who earn more than their managers.
```sql
-- Question: Complex self-join with salary comparison
SELECT e1.name as employee, e1.salary as emp_salary,
       e2.name as manager, e2.salary as mgr_salary
FROM employees e1
INNER JOIN employees e2 ON e1.manager_id = e2.emp_id
WHERE e1.salary > e2.salary;
```

---

## 3. Aggregate Functions

### **Syntax & Examples**

```sql
-- Basic aggregates
SELECT COUNT(*) FROM employees;
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT MIN(salary) FROM employees;

-- With GROUP BY
SELECT department, COUNT(*), AVG(salary)
FROM employees
GROUP BY department;

-- With HAVING
SELECT department, AVG(salary) as avg_sal
FROM employees
GROUP BY department
HAVING AVG(salary) > 65000;
```

### **Real-Time Scenarios & Questions**

#### **Easy Level**
**Scenario**: Find total number of employees and average salary.
```sql
-- Question: Basic statistics
SELECT COUNT(*) as total_employees, 
       AVG(salary) as average_salary,
       MAX(salary) as highest_salary
FROM employees;
```

#### **Medium Level**
**Scenario**: Department-wise salary analysis.
```sql
-- Question: Comprehensive department analysis
SELECT department,
       COUNT(*) as employee_count,
       AVG(salary) as avg_salary,
       MAX(salary) as max_salary,
       MIN(salary) as min_salary
FROM employees
GROUP BY department
ORDER BY avg_salary DESC;
```

#### **Hard Level**
**Scenario**: Find departments where average salary is above company average.
```sql
-- Question: Subquery with aggregates
SELECT department, AVG(salary) as dept_avg
FROM employees
GROUP BY department
HAVING AVG(salary) > (SELECT AVG(salary) FROM employees);
```

---

## 4. Window Functions

### **Syntax & Examples**

#### ROW_NUMBER, RANK, DENSE_RANK
```sql
SELECT name, salary, department,
       ROW_NUMBER() OVER (ORDER BY salary DESC) as row_num,
       RANK() OVER (ORDER BY salary DESC) as rank_val,
       DENSE_RANK() OVER (ORDER BY salary DESC) as dense_rank_val
FROM employees;

-- Partition by department
SELECT name, salary, department,
       ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as dept_rank
FROM employees;
```

#### LEAD and LAG
```sql
SELECT name, salary,
       LAG(salary) OVER (ORDER BY hire_date) as prev_salary,
       LEAD(salary) OVER (ORDER BY hire_date) as next_salary
FROM employees;
```

#### FIRST_VALUE and LAST_VALUE
```sql
SELECT name, salary, department,
       FIRST_VALUE(salary) OVER (PARTITION BY department ORDER BY salary DESC) as highest_in_dept,
       LAST_VALUE(salary) OVER (PARTITION BY department ORDER BY salary DESC 
                               ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) as lowest_in_dept
FROM employees;
```

### **Real-Time Scenarios & Questions**

#### **Easy Level**
**Scenario**: Rank employees by salary within each department.
```sql
-- Question: Department-wise salary ranking
SELECT name, department, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dept_salary_rank
FROM employees;
```

#### **Medium Level**
**Scenario**: Find salary difference between consecutive employees by hire date.
```sql
-- Question: Using LAG function
SELECT name, hire_date, salary,
       salary - LAG(salary) OVER (ORDER BY hire_date) as salary_diff_from_prev
FROM employees
ORDER BY hire_date;
```

#### **Hard Level**
**Scenario**: Calculate running total of salaries and percentage of total budget.
```sql
-- Question: Complex window function usage
SELECT name, salary,
       SUM(salary) OVER (ORDER BY hire_date ROWS UNBOUNDED PRECEDING) as running_total,
       ROUND(salary * 100.0 / SUM(salary) OVER (), 2) as pct_of_total_budget
FROM employees
ORDER BY hire_date;
```

---

## 5. Subqueries

### **Syntax & Types**

#### Scalar Subquery
```sql
-- Single value subquery
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

#### Correlated Subquery
```sql
-- Subquery referencing outer query
SELECT name, salary, department
FROM employees e1
WHERE salary > (SELECT AVG(salary) 
                FROM employees e2 
                WHERE e2.department = e1.department);
```

#### EXISTS
```sql
SELECT name FROM employees e
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = e.emp_id);
```

#### IN/NOT IN
```sql
SELECT name FROM employees
WHERE department IN (SELECT dept_name FROM departments WHERE location = 'New York');
```

### **Real-Time Scenarios & Questions**

#### **Easy Level**
**Scenario**: Find employees earning above average salary.
```sql
-- Question: Simple subquery
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

#### **Medium Level**
**Scenario**: Find employees who earn the highest salary in their department.
```sql
-- Question: Correlated subquery
SELECT name, department, salary
FROM employees e1
WHERE salary = (SELECT MAX(salary) 
                FROM employees e2 
                WHERE e2.department = e1.department);
```

#### **Hard Level**
**Scenario**: Find departments with no employees hired in the last year.
```sql
-- Question: Complex EXISTS with date logic
SELECT dept_name
FROM departments d
WHERE NOT EXISTS (
    SELECT 1 FROM employees e
    WHERE e.department = d.dept_name
    AND e.hire_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
);
```

---

## 6. Set Operations

### **Syntax & Examples**

#### UNION / UNION ALL
```sql
-- UNION (removes duplicates)
SELECT name FROM employees WHERE department = 'IT'
UNION
SELECT name FROM employees WHERE salary > 70000;

-- UNION ALL (keeps duplicates)
SELECT department FROM employees
UNION ALL
SELECT dept_name FROM departments;
```

#### INTERSECT
```sql
-- Common records (MySQL doesn't support INTERSECT directly)
SELECT name FROM employees WHERE department = 'IT'
AND name IN (SELECT name FROM employees WHERE salary > 70000);
```

#### EXCEPT/MINUS
```sql
-- Records in first query but not in second (MySQL alternative)
SELECT name FROM employees WHERE department = 'IT'
AND name NOT IN (SELECT name FROM employees WHERE salary > 70000);
```

### **Real-Time Scenarios & Questions**

#### **Easy Level**
**Scenario**: Get all unique department names from both tables.
```sql
-- Question: Basic UNION
SELECT department as dept_name FROM employees
UNION
SELECT dept_name FROM departments;
```

#### **Medium Level**
**Scenario**: Find employees in IT who don't earn above 70k.
```sql
-- Question: Using set operations concept
SELECT name FROM employees WHERE department = 'IT'
AND name NOT IN (SELECT name FROM employees WHERE salary > 70000);
```

#### **Hard Level**
**Scenario**: Compare employee lists between two time periods.
```sql
-- Question: Complex set operation
SELECT 'Only in 2022' as category, name
FROM employees WHERE YEAR(hire_date) = 2022
AND name NOT IN (SELECT name FROM employees WHERE YEAR(hire_date) = 2021)

UNION ALL

SELECT 'Only in 2021' as category, name
FROM employees WHERE YEAR(hire_date) = 2021
AND name NOT IN (SELECT name FROM employees WHERE YEAR(hire_date) = 2022);
```

---

## 7. Common Table Expressions (CTEs)

### **Syntax & Examples**

#### Basic CTE
```sql
WITH dept_stats AS (
    SELECT department, AVG(salary) as avg_salary, COUNT(*) as emp_count
    FROM employees
    GROUP BY department
)
SELECT * FROM dept_stats WHERE avg_salary > 65000;
```

#### Recursive CTE
```sql
-- Employee hierarchy
WITH RECURSIVE emp_hierarchy AS (
    -- Base case: top-level managers
    SELECT emp_id, name, manager_id, 0 as level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case
    SELECT e.emp_id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    INNER JOIN emp_hierarchy eh ON e.manager_id = eh.emp_id
)
SELECT * FROM emp_hierarchy ORDER BY level, name;
```

### **Real-Time Scenarios & Questions**

#### **Easy Level**
**Scenario**: Simplify complex query with CTE.
```sql
-- Question: Basic CTE usage
WITH high_earners AS (
    SELECT * FROM employees WHERE salary > 70000
)
SELECT department, COUNT(*) as high_earner_count
FROM high_earners
GROUP BY department;
```

#### **Medium Level**
**Scenario**: Multi-level analysis with multiple CTEs.
```sql
-- Question: Multiple CTEs
WITH dept_avg AS (
    SELECT department, AVG(salary) as avg_sal
    FROM employees GROUP BY department
),
company_avg AS (
    SELECT AVG(salary) as company_avg_sal FROM employees
)
SELECT d.department, d.avg_sal, c.company_avg_sal,
       d.avg_sal - c.company_avg_sal as difference
FROM dept_avg d
CROSS JOIN company_avg c;
```

#### **Hard Level**
**Scenario**: Build organizational hierarchy with levels.
```sql
-- Question: Recursive CTE for hierarchy
WITH RECURSIVE org_chart AS (
    SELECT emp_id, name, manager_id, 0 as level, name as path
    FROM employees WHERE manager_id IS NULL
    
    UNION ALL
    
    SELECT e.emp_id, e.name, e.manager_id, oc.level + 1,
           CONCAT(oc.path, ' -> ', e.name) as path
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.emp_id
)
SELECT level, name, path FROM org_chart ORDER BY level, name;
```

---

## 8. Advanced Topics

### **Indexes**

#### Creating Indexes
```sql
-- Single column index
CREATE INDEX idx_emp_dept ON employees(department);

-- Composite index
CREATE INDEX idx_emp_dept_sal ON employees(department, salary);

-- Unique index
CREATE UNIQUE INDEX idx_emp_email ON employees(email);
```

### **Views**

#### Creating Views
```sql
-- Simple view
CREATE VIEW high_earners AS
SELECT name, department, salary
FROM employees
WHERE salary > 70000;

-- Complex view with joins
CREATE VIEW emp_dept_view AS
SELECT e.name, e.salary, d.dept_name, d.location
FROM employees e
JOIN departments d ON e.department = d.dept_name;
```

### **Stored Procedures**

```sql
DELIMITER //
CREATE PROCEDURE GetEmployeesByDept(IN dept_name VARCHAR(50))
BEGIN
    SELECT name, salary, hire_date
    FROM employees
    WHERE department = dept_name
    ORDER BY salary DESC;
END //
DELIMITER ;

-- Call procedure
CALL GetEmployeesByDept('IT');
```

### **Triggers**

```sql
DELIMITER //
CREATE TRIGGER salary_audit
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    IF NEW.salary != OLD.salary THEN
        INSERT INTO salary_audit_log(emp_id, old_salary, new_salary, change_date)
        VALUES(NEW.emp_id, OLD.salary, NEW.salary, NOW());
    END IF;
END //
DELIMITER ;
```

---

## 9. Performance Optimization Tips

### **Query Optimization Techniques**

1. **Use indexes on frequently queried columns**
2. **Avoid SELECT * in production queries**
3. **Use LIMIT for large datasets**
4. **Optimize JOIN conditions**
5. **Use EXISTS instead of IN for subqueries**
6. **Avoid functions in WHERE clauses**

### **Example: Before and After Optimization**

#### Before (Slow)
```sql
SELECT * FROM employees 
WHERE YEAR(hire_date) = 2022;
```

#### After (Fast)
```sql
SELECT emp_id, name, department, salary 
FROM employees 
WHERE hire_date >= '2022-01-01' 
AND hire_date < '2023-01-01';
```

---

## 10. Common Interview Questions

### **Easy Questions**
1. What's the difference between DELETE and TRUNCATE?
2. What are the types of JOINs?
3. What's the difference between WHERE and HAVING?

### **Medium Questions**
1. Explain the difference between RANK() and DENSE_RANK()
2. What are correlated subqueries?
3. How do you find duplicate records?

### **Hard Questions**
1. Write a query to find the second highest salary in each department
2. Explain query execution plan and optimization
3. How would you handle a slowly changing dimension?

---

## 11. Practice Scenarios

### **E-commerce Database Queries**

#### Find top 5 customers by total order value
```sql
SELECT c.customer_name, SUM(o.total_amount) as total_spent
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
ORDER BY total_spent DESC
LIMIT 5;
```

#### Monthly sales trend
```sql
SELECT 
    DATE_FORMAT(order_date, '%Y-%m') as month,
    COUNT(*) as order_count,
    SUM(total_amount) as total_sales
FROM orders
WHERE order_date >= DATE_SUB(CURDATE(), INTERVAL 12 MONTH)
GROUP BY DATE_FORMAT(order_date, '%Y-%m')
ORDER BY month;
```

### **HR Database Queries**

#### Employee retention analysis
```sql
WITH tenure_analysis AS (
    SELECT 
        emp_id,
        name,
        DATEDIFF(CURDATE(), hire_date) / 365.25 as years_worked,
        CASE 
            WHEN DATEDIFF(CURDATE(), hire_date) / 365.25 < 1 THEN 'New'
            WHEN DATEDIFF(CURDATE(), hire_date) / 365.25 < 3 THEN 'Junior'
            WHEN DATEDIFF(CURDATE(), hire_date) / 365.25 < 5 THEN 'Mid-level'
            ELSE 'Senior'
        END as tenure_category
    FROM employees
)
SELECT tenure_category, COUNT(*) as employee_count
FROM tenure_analysis
GROUP BY tenure_category;
```

This comprehensive guide covers all major SQL concepts with progressive difficulty levels and real-world scenarios that are commonly asked in interviews.
