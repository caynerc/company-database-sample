# Portfolio Project: Database and SQL

## Getting started
This is a guided Database and SQL project from [freeCodeCamp.org](https://youtu.be/HXV3zeQKqGY), with the intent to apply what I've learned from [Google Data Analytics Professional Certificate course](https://coursera.org/share/7f838c9094e1d5e3e22d61fbce136142) and just get started with maneuvering with data.

Using this sample company database of a hypothetical company, I hope to showcase SQL and database skills such as the following:

- [x] Database schemas and Entitry Relationship diagrams (ERD)
- [x] Tables and keys
- [x] Create, Read, Update, Delete (CRUD)
- [x] SQL functions
- [x] Union and Joins
- [x] Nested queries

## Table of Contents

- [About the data](#about-the-data)
- [Prerequisites](#prerequisites)
- [Database creation](#database-creation)
- [Tables creation](#tables-creation)
- [Data insertion](#data-insertion)
- [SQL basic queries](#sql-basic-queries)
- [SQL functions](#sql-functions)
- [SQL patterns](#sql-patterns)
- [SQL UNION and JOINS](#sql-union-and-joins)
- [SQL nested queries](#sql-nested-queries)
- [Conclusion](#conclusion)

## About the data

Here's the ERD of the database:

![Company ERD](https://www.mikedane.com/databases/sql/company-erd.png)

For reference, this is the database we will recreate:

![Company database schema](https://www.mikedane.com/databases/sql/company-database.png)

## Prerequisites

- MySQL Community Server, [download link here](https://dev.mysql.com/downloads/windows/installer/8.0.html).
- PopSQL, [download link here](https://get.popsql.com/).

## Database creation

In mySQL Command Line Cient:

```sql
CREATE DATABASE sample_company_database;
```

## Tables creation

After linking our 'sample_company_database' to PopSQL, we make the tables in PopSQL based on our ERD:

```sql
-- Create Staff table 
CREATE TABLE staff (
    PRIMARY KEY (staff_id),
    staff_id        INT         NOT NULL,
    first_name      VARCHAR(40) NOT NULL,
    last_name       VARCHAR(40) NOT NULL,
    birthday        DATE        NOT NULL,
    sex             VARCHAR(1)  NOT NULL,
    salary          INT         NOT NULL,
    supervisor_id   INT,
    branch_id       INT
);

-- Create Branch table 
CREATE TABLE branch (
    PRIMARY KEY (branch_id),
    branch_id       INT         AUTO_INCREMENT,
    branch_name     VARCHAR(20) NOT NULL,
    manager_id      INT,
    manager_start_date DATE,
    FOREIGN KEY (manager_id) REFERENCES staff (staff_id) ON DELETE SET NULL -- If a row in 'staff.staff_id' is deleted, 'branch.manager_id' will be set to NULL
 );

-- Make 'staff.branch_id' a foreign key 
ALTER TABLE staff
ADD FOREIGN KEY (branch_id) REFERENCES branch (branch_id) ON DELETE SET NULL; -- If a row in 'staff.branch_id' is deleted, 'branch.branch_id' will be set to NULL

-- Make 'staff.supervisor_id' a foreign key 
ALTER TABLE staff
ADD FOREIGN KEY (supervisor_id) REFERENCES staff (staff_id) ON DELETE SET NULL; -- If a row in 'staff.staff_id' is deleted, 'staff.supervisor_id' will be set to NULL

-- Create Client table 
CREATE TABLE client (
    PRIMARY KEY (client_id),
    client_id       INT         AUTO_INCREMENT,
    client_name     VARCHAR(99) NOT NULL,
    branch_id       INT,
    FOREIGN KEY (branch_id) REFERENCES branch (branch_id) ON DELETE SET NULL -- If a row in 'branch.branch_id' is deleted, 'client.branch_id' will be set to NULL
);

-- Create Works With table 
CREATE TABLE works_with (
    PRIMARY KEY (staff_id, client_id),
    staff_id        INT,
    client_id       INT,
    total_sales     INT,
    FOREIGN KEY (staff_id) REFERENCES staff (staff_id) ON DELETE CASCADE,
    FOREIGN KEY (client_id) REFERENCES client (client_id) ON DELETE CASCADE
);

-- Create Branch Supplier table 
CREATE TABLE branch_supplier (
    PRIMARY KEY (branch_id, supplier_name),
    branch_id       INT,
    supplier_name   VARCHAR(255),
    supply_type     VARCHAR(255),
    FOREIGN KEY (branch_id) REFERENCES branch (branch_id) ON DELETE CASCADE
);

-- Add data Staff table 
INSERT INTO staff VALUES (
    100,
    'David',
    'Wallace',
    '1967-11-17',
    'M',
    250000,
    NULL,
    NULL    -- NULL because 'branch_id = 1' is not yet created
);

-- Create Branch table 
INSERT INTO  branch(branch_name, manager_id, manager_start_date) VALUES (
    'Corporate',
    '100',
    '2006-02-09'

-- Update 'staff.staff_id = 100' with 'branch_id = 1'
UPDATE staff
SET branch_id = 1
WHERE staff_id = 100;
);
```

## Data insertion

With tables and keys all set, we are now ready for populating the table with data. In PopSQL:
```sql
-- Add new data to Staff table 
INSERT INTO staff VALUES (
    101,
    'Jan',
    'Levinson',
    '1961-05-11',
    'F',
    110000,
    100,
    1
);

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    102,
    'Michael',
    'Scott',
    '1964-03-15',
    'M',
    75000,
    100,
    NULL    -- NULL because 'branch_id = 2' is not yet created
);

-- Add new data to Branch table 
INSERT INTO branch VALUES (
    2,
    'Scranton',
    102,
    '1992-04-06'
);

-- Update 'staff.staff_id = 102' with 'branch_id = 2' 
UPDATE staff
SET branch_id = 2
WHERE staff_id = 102;

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    103,
    'Angela',
    'Martin',
    '1971-06-25',
    'F',
    63000,
    102,
    2
);

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    103,
    'Angela',
    'Martin',
    '1971-06-25',
    'F',
    63000,
    102,
    2
);

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    104,
    'Kelly',
    'Kapoor',
    '1980-02-05',
    'F',
    55000,
    102,
    2
);

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    105,
    'Stanley',
    'Hudson',
    '1958-02-19',
    'M',
    69000,
    102,
    2
);

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    106,
    'Josh',
    'Porter',
    '1969-09-05',
    'M',
    78000,
    100,
    NULL    -- NULL because 'branch_id = 3' is not yet created
);

-- Add new data to Branch table 
INSERT INTO branch VALUES (
    3,
    'Stamford',
    106,
    '1998-02-13'
);

-- Update 'staff.staff_id = 106' with 'branch_id = 3' 
UPDATE staff
SET branch_id = 3
WHERE staff_id = 106;

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    107,
    'Andy',
    'Bernard',
    '1973-07-22',
    'M',
    65000,
    106,
    3
);

-- Add new data to Staff table 
INSERT INTO staff VALUES (
    108,
    'Jim',
    'Halpert',
    '1978-10-01',
    'M',
    71000,
    106,
    3
);

-- Add data to Client table 
INSERT INTO client VALUES (
    400,
    'Dunmore Highschool',
    2
);

INSERT INTO client VALUES (
    401,
    'Lackawana Country',
    2
);

INSERT INTO client VALUES (
    402,
    'FedEx',
    3
);

INSERT INTO client VALUES (
    403,
    'John Daly Law, LLC',
    3
);

INSERT INTO client VALUES (
    404,
    'Scranton Whitepages',
    2
);

INSERT INTO client VALUES (
    405,
    'Times Newspaper',
    3
);

INSERT INTO client VALUES (
    406,
    'FedEx',
    2
);

-- Add data to Branch Supplier table 
INSERT INTO branch_supplier VALUES (
    2,
    'Hammer Mill',
    'Paper'
);

INSERT INTO branch_supplier VALUES (
    2,
    'Uni-ball',
    'Writing utensils'
);

INSERT INTO branch_supplier VALUES (
    3,
    'Patriot Paper',
    'Paper'
);

INSERT INTO branch_supplier VALUES (
    2,
    'J.T. Forms and Labels',
    'Custom forms'
);

INSERT INTO branch_supplier VALUES (
    3,
    'Uni-ball',
    'Writing utensils'
);

INSERT INTO branch_supplier VALUES (
    3,
    'Hammer Mill',
    'Paper'
);

INSERT INTO branch_supplier VALUES (
    3,
    'Stamford Labels',
    'Custom forms'
);


-- Add data to Works With table 
INSERT INTO works_with VALUES (
    105,
    400,
    55000
);

INSERT INTO works_with VALUES (
    105,
    402,
    267000
);

INSERT INTO works_with VALUES (
    108,
    402,
    22500
);

INSERT INTO works_with VALUES (
    107,
    403,
    5000
);

INSERT INTO works_with VALUES (
    108,
    403,
    12000
);

INSERT INTO works_with VALUES (
    105,
    404,
    33000
);

INSERT INTO works_with VALUES (
    107,
    405,
    15000
);

INSERT INTO works_with VALUES (
    102,
    406,
    15000
);

INSERT INTO works_with VALUES (
    105,
    406,
    130000
);
```

## SQL basic queries

Now with data to play with, we perform basic SQL queries such as:
<br>

---

**Task: Find all employees**
```sql
SELECT *
FROM staff;
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|--------|---------------|------------|
| 100      | David      | Wallace    | 1967-11-17 | M   | 250000 | NULL          | 1          |
| 101      | Jan        | Levinson   | 1961-05-11 | F   | 110000 | 100           | 1          |
| 102      | Michael    | Scott      | 1964-03-15 | M   | 75000  | 100           | NULL       |
| 103      | Angela     | Martin     | 1971-06-25 | F   | 63000  | 102           | 2          |
| 104      | Kelly      | Kapoor     | 1980-02-05 | F   | 55000  | 102           | 2          |
| 105      | Stanley    | Hudson     | 1958-02-19 | M   | 69000  | 102           | 2          |
| 106      | Josh       | Porter     | 1969-09-05 | M   | 78000  | 100           | 3          |
| 107      | Andy       | Bernard    | 1973-07-22 | M   | 65000  | 106           | 3          |
| 108      | Jim        | Halpert    | 1978-10-01 | M   | 71000  | 106           | 3          |

<br>

---

**Task: Find all clients**
```sql  
SELECT *
FROM client;
```

*Output*

| client_id | client_name          | branch_id |
|-----------|----------------------|-----------|
| 400       | Dunmore Highschool   | 2         |
| 401       | Lackawana Country    | 2         |
| 402       | FedEx                | 3         |
| 403       | John Daly Law, LLC   | 3         |
| 404       | Scranton Whitepages  | 2         |
| 405       | Times Newspaper      | 3         |
| 406       | FedEx                | 2         |

<br>

---

**Task: Find the top 5 employees with the highest salaries**
```sql
SELECT
    salary,
    first_name,
    last_name
FROM staff
ORDER BY salary DESC
LIMIT 5;
```
*Output:*
| salary  | first_name | last_name  |
|---------|------------|------------|
| 250000  | David      | Wallace    |
| 110000  | Jan        | Levinson   |
| 78000   | Josh       | Porter     |
| 75000   | Michael    | Scott      |
| 71000   | Jim        | Halpert    |

<br>

---

**Task: Find all employees ordered by sex then name**
```sql
SELECT
    sex,
    CONCAT (first_name, ' ', last_name) AS full_name
FROM staff
ORDER BY 
    sex,
    full_name;
```
*Output:*
| sex | full_name        |
|-----|-----------------|
| F   | Angela Martin   |
| F   | Jan Levinson    |
| F   | Kelly Kapoor    |
| M   | Andy Bernard    |
| M   | David Wallace   |
| M   | Jim Halpert     |
| M   | Josh Porter     |
| M   | Michael Scott   |
| M   | Stanley Hudson  |

<br>

---

**Task: Find the first 5 employees in the table**
```sql
SELECT *
FROM staff
LIMIT 5;
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary  | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 100      | David      | Wallace    | 1967-11-17 | M   | 250000  | NULL          | 1          |
| 101      | Jan        | Levinson   | 1961-05-11 | F   | 110000  | 100           | 1          |
| 102      | Michael    | Scott      | 1964-03-15 | M   | 75000   | 100           | NULL       |
| 103      | Angela     | Martin     | 1971-06-25 | F   | 63000   | 102           | 2          |
| 104      | Kelly      | Kapoor     | 1980-02-05 | F   | 55000   | 102           | 2          |

<br>

---

**Task: Find the first and last names of all employees**
```sql
SELECT first_name, last_name
FROM staff;
```
*Output:*
| first_name | last_name  |
|------------|------------|
| David      | Wallace    |
| Jan        | Levinson   |
| Michael    | Scott      |
| Angela     | Martin     |
| Kelly      | Kapoor     |
| Stanley    | Hudson     |
| Josh       | Porter     |
| Andy       | Bernard    |
| Jim        | Halpert    |

<br>

---

**Task: Find the forename and surnames names of all employees**
```sql
SELECT
    first_name AS forename,
    last_name AS surname
FROM staff;
```
*Output:*
| forename | surname  |
|----------|----------|
| David    | Wallace  |
| Jan      | Levinson |
| Michael  | Scott    |
| Angela   | Martin   |
| Kelly    | Kapoor   |
| Stanley  | Hudson   |
| Josh     | Porter   |
| Andy     | Bernard  |
| Jim      | Halpert  |

<br>

---

**Task: Find all the different genders**
```sql
SELECT DISTINCT sex
FROM staff;
```
*Output:*
| sex |
|-----|
| M   |
| F   |

<br>

---

**Task: Find all male employees**
```sql
SELECT *
FROM staff
WHERE sex = 'M';
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary  | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 100      | David      | Wallace    | 1967-11-17 | M   | 250000  |               | 1          |
| 102      | Michael    | Scott      | 1964-03-15 | M   | 75000   | 100           |            |
| 105      | Stanley    | Hudson     | 1958-02-19 | M   | 69000   | 102           | 2          |
| 106      | Josh       | Porter     | 1969-09-05 | M   | 78000   | 100           | 3          |
| 107      | Andy       | Bernard    | 1973-07-22 | M   | 65000   | 106           | 3          |
| 108      | Jim        | Halpert    | 1978-10-01 | M   | 71000   | 106           | 3          |

<br>

---

**Task: Find all employees at Branch 2**
```sql
SELECT *
FROM staff
WHERE branch_id = 2;
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary  | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 103      | Angela     | Martin     | 1971-06-25 | F   | 63000   | 102           | 2          |
| 104      | Kelly      | Kapoor     | 1980-02-05 | F   | 55000   | 102           | 2          |
| 105      | Stanley    | Hudson     | 1958-02-19 | M   | 69000   | 102           | 2          |

<br>

---

**Task: Find all employee born after 1969**
```sql
SELECT *
FROM staff
WHERE birthday > '1969-12-12';
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary  | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 103      | Angela     | Martin     | 1971-06-25 | F   | 63000   | 102           | 2          |
| 104      | Kelly      | Kapoor     | 1980-02-05 | F   | 55000   | 102           | 2          |
| 107      | Andy       | Bernard    | 1973-07-22 | M   | 65000   | 106           | 3          |
| 108      | Jim        | Halpert    | 1978-10-01 | M   | 71000   | 106           | 3          |
<br>

---

**Task: Find all female employees at Branch 2**
```sql
SELECT *
FROM staff
WHERE branch_id = 2
    AND sex = 'F';
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary  | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 103      | Angela     | Martin     | 1971-06-25 | F   | 63000   | 102           | 2          |
| 104      | Kelly      | Kapoor     | 1980-02-05 | F   | 55000   | 102           | 2          |

<br>

---

**Task: Find all employees who are female & born after 1969 or who make over 80000**
```sql
SELECT *
FROM staff
WHERE (
    sex = 'F'
    AND birthday > '1969-12-12')
    OR salary > 80000;
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary  | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 100      | David      | Wallace    | 1967-11-17 | M   | 250000  |               | 1          |
| 101      | Jan        | Levinson   | 1961-05-11 | F   | 110000  | 100           | 1          |
| 103      | Angela     | Martin     | 1971-06-25 | F   | 63000   | 102           | 2          |
| 104      | Kelly      | Kapoor     | 1980-02-05 | F   | 55000   | 102           | 2          |

<br>

---

**Task: Find all employees born between 1970 and 1975**
```sql
SELECT *
FROM staff
WHERE birthday 
    BETWEEN '1970-01-01'
    AND '1975-01-01';
```
*Output:*
| staff_id | first_name | last_name  | birthday   | sex | salary  | supervisor_id | branch_id  |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 103      | Angela     | Martin     | 1971-06-25 | F   | 63000   | 102           | 2          |
| 107      | Andy       | Bernard    | 1973-07-22 | M   | 65000   | 106           | 3          |

<br>

---

**Task: Find all employees named Jim, Michael, Johnny or David**
```sql
SELECT *
FROM staff
WHERE first_name
    IN ('Jim', 'Michael', 'John', 'David');
```
*Output:*
| staff_id | first_name | last_name | birthday   | sex | salary  | supervisor_id | branch_id |
|----------|------------|------------|------------|-----|---------|---------------|------------|
| 100      | David      | Wallace    | 1967-11-17 | M   | 250000  | NULL          | 1          |
| 102      | Michael    | Scott      | 1964-03-15 | M   | 75000   | 100           | NULL       |
| 108      | Jim        | Halpert    | 1978-10-01 | M   | 71000   | 106           | 3          |

<br>

---


## SQL functions

Using SQL functions, we perform basic operations
<br>

---

**Task: Find the number of employees**
```sql
SELECT COUNT (staff_id) AS staff_count
FROM staff;
```
*Output:*
| staff_count |
|-------------|
| 9           |

<br>

---

**Task: Find the average of all employees' salaries**
```sql
SELECT AVG (salary) AS average_salary
FROM staff;
```
*Output:*
| average_salary |
|----------------|
| 92888.8889     |

<br>

---

**Task: Find the sum of all employee's salaries**
```sql
SELECT SUM (salary) AS total_salary
FROM staff;
```
*Output:*
| total_salary |
|--------------|
| 836000       |

<br>

---

**Task: Find out how many males and females there are**
```sql
SELECT COUNT (sex = 'M') AS male_count,
    COUNT (sex = 'F') AS female_count
FROM staff;
```
*Output:*
| category    | count |
|-------------|-------|
| male_count  | 9     |
| female_count| 9     |

<br>

---

**Task: Find the total sales of each salesman**
```sql
SELECT 
    works_with.staff_id,
    CONCAT (staff.first_name, ' ', staff.last_name) AS full_name,
    SUM (works_with.total_sales) AS total_sales
FROM works_with
INNER JOIN staff
    ON works_with.staff_id = staff.staff_id
GROUP BY staff.staff_id;
```
*Output:*
| staff_id | full_name      | total_sales |
|----------|----------------|-------------|
| 102      | Michael Scott  | 282000      |
| 105      | Stanley Hudson | 218000      |
| 107      | Andy Bernard   | 31000       |
| 108      | Jim Halpert    | 34500       |

<br>

---

**Task: Find the total amount of money spent by each client**
```sql
SELECT
    works_with.client_id,
    client.client_name,
    SUM (works_with.total_sales) AS total_spent_by_client
FROM works_with
INNER JOIN client
    ON works_with.client_id = client.client_id
GROUP BY works_with.client_id;
```
*Output:*
| client_id | client_name            | total_spent_by_client |
|-----------|------------------------|-----------------------|
| 400       | Dunmore Highschool     | 55000                 |
| 401       | Lackawana Country      | 267000                |
| 402       | FedEx                  | 22500                 |
| 403       | John Daly Law, LLC     | 17000                 |
| 404       | Scranton Whitepages    | 33000                 |
| 405       | Times Newspaper        | 26000                 |
| 406       | FedEx                  | 145000                |

<br>

---

**Task: Correction with data**
```sql
SELECT *
FROM works_with;

DELETE FROM works_with
WHERE total_sales = 15000;

INSERT INTO works_with VALUES(
    107,
    405,
    26000
);

INSERT INTO works_with VALUES(
    102,
    406,
    15000
);
```
*Output:*
| staff_id | client_id | total_sales |
|----------|-----------|-------------|
| 102      | 401       | 267000      |
| 102      | 406       | 15000       |
| 105      | 400       | 55000       |
| 105      | 404       | 33000       |
| 105      | 406       | 130000      |
| 107      | 403       | 5000        |
| 107      | 405       | 26000       |
| 108      | 402       | 22500       |
| 108      | 403       | 12000       |

<br>

---

## SQL patterns
Exercises for using LIKE function.

<br>

---

**Task: Find any client's who are an LLC**
```sql
SELECT *
FROM client
WHERE client_name LIKE '%LLC';
```
*Output:*
| client_id | client_name            | branch_id |
|-----------|------------------------|-----------|
| 403       | John Daly Law, LLC     | 3         |

<br>

---

**Task: Find any branch suppliers who are in the label business**
```sql
SELECT *
FROM branch_supplier
WHERE supplier_name LIKE '%label%';
```
*Output:*
| branch_id | supplier_name         | supply_type   |
|-----------|-----------------------|---------------|
| 2         | J.T. Forms and Labels | Custom forms  |
| 3         | Stamford Labels       | Custom forms  |

<br>

---


**Task: Find any employee born on the 17th day of the month**
```sql
SELECT *
FROM staff
WHERE birthday LIKE '%17';
```
*Output:*
| staff_id | first_name | last_name | birthday       | sex | salary  | supervisor_id | branch_id |
|----------|------------|------------|----------------|-----|---------|---------------|------------|
| 100      | David      | Wallace    | 1967-11-17     | M   | 250000  | NULL          | 1          |

<br>

--- 

**Task: Find any clients who are schools**
```sql
SELECT *
FROM client
WHERE client_name LIKE '%school';
```
*Output:*
| client_id | client_name        | branch_id |
|-----------|--------------------|-----------|
| 400       | Dunmore Highschool | 2         |

<br>

---


## SQL UNION and JOINS
Exercises for using UNION and JOINS function.
<br>

---

**Task: Find a list of employee and branch names**
```sql
SELECT first_name FROM staff
UNION
SELECT branch_name FROM branch;
```
*Output:*
| first_name |
|------------|
| David      |
| Jan        |
| Michael    |
| Angela     |
| Kelly      |
| Stanley    |
| Josh       |
| Andy       |
| Jim        |
| Corporate  |
| Scranton   |
| Stamford   |
| Buffalo    |

<br>

---

**Task: Find a list of all clients & branch suppliers' names**
```sql
SELECT client_name FROM client
UNION
SELECT branch_name FROM branch;
```
*Output:*
| client_name            |
|------------------------|
| Dunmore Highschool     |
| Lackawana Country      |
| FedEx                  |
| John Daly Law, LLC     |
| Scranton Whitepages    |
| Times Newspaper        |
| Corporate              |
| Scranton               |
| Stamford               |
| Buffalo                |

<br>

---

**Task: Add the extra branch**
```sql
INSERT INTO branch VALUES (
    4,
    'Buffalo',
    NULL,
    NULL
);

SELECT
    staff.staff_id,
    staff.first_name,
    staff.last_name,
    branch.branch_name
FROM staff
RIGHT JOIN branch
    ON staff.staff_id = branch.manager_id;    -- Previews the table
```
*Output:*
| staff_id | first_name | last_name  | branch_name |
|----------|------------|------------|-------------|
| 100      | David      | Wallace    | Corporate   |
| 102      | Michael    | Scott      | Scranton    |
| 106      | Josh       | Porter     | Stamford    |
| NULL     | NULL       | NULL       | Buffalo     |

<br>

---

## SQL nested queries
Exercises for using nested queries.
<br>

---

**Task: Find names of all employees who have sold over 50,000**
```sql
SELECT 
    staff.staff_id,
    CONCAT (staff.first_name, ' ', staff.last_name) AS full_name,
    SUM (works_with.total_sales) AS total_sales_per_staff
FROM staff
INNER JOIN works_with
    ON staff.staff_id = works_with.staff_id
WHERE staff.staff_id IN (
    SELECT works_with.staff_id
    FROM works_with
    WHERE works_with.total_sales > 50000
)
GROUP BY staff.staff_id;
```
*Output:*
| staff_id | full_name          | total_sales_per_staff |
|----------|--------------------|-----------------------|
| 102      | Michael Scott      | 282000                |
| 105      | Stanley Hudson     | 218000                |


*Alternative solution using JOIN*
```sql
SELECT 
    works_with.staff_id,
    works_with.total_sales,
    staff.first_name
FROM works_with
INNER JOIN staff
    ON works_with.staff_id = staff.staff_id
WHERE works_with.total_sales > 50000;
```
*Output:*
| staff_id | total_sales | first_name |
|----------|-------------|------------|
| 102      | 267000      | Michael    |
| 105      | 55000       | Stanley    |
| 105      | 130000      | Stanley    |

<br>

---

**Task: Find all clients who are handles by the branch that Michael Scott manages
(assume you know Michael's ID)**
```sql
SELECT
    client.branch_id,
    client.client_name
FROM client
WHERE client.branch_id IN (
    SELECT branch.branch_id
    FROM branch
    WHERE branch.branch_id = 2
);
```
*Output:*
| branch_id | client_name         |
|-----------|---------------------|
| 2         | Dunmore Highschool  |
| 2         | Lackawana Country   |
| 2         | Scranton Whitepages |
| 2         | FedEx               |

<br>

---

**Task: Find all clients who are handles by the branch that Michael Scott manages
(assume you DON'T know Michael's ID)**
```sql
SELECT
    staff.staff_id,
    CONCAT (staff.first_name, ' ', staff.last_name) AS full_name,
    client.branch_id,
    client.client_name
FROM client
JOIN branch
    ON branch.branch_id = client.branch_id
JOIN staff
    ON staff.staff_id = branch.manager_id
WHERE client.branch_id = (
    SELECT branch.branch_id
    FROM branch
    WHERE branch.manager_id = (
        SELECT staff.staff_id
        FROM staff
        WHERE first_name = 'Michael'
    )
);
```
*Output:*
| staff_id | full_name     | branch_id | client_name         |
|----------|---------------|-----------|---------------------|
| 102      | Michael Scott | 2         | Dunmore Highschool  |
| 102      | Michael Scott | 2         | Lackawana Country   |
| 102      | Michael Scott | 2         | Scranton Whitepages |
| 102      | Michael Scott | 2         | FedEx               |

<br>

---

**Task: Find the names of employees who work with clients handled by the Scranton branch**
```sql
SELECT
    staff.staff_id,
    CONCAT (staff.first_name, ' ', staff.last_name) AS full_name
FROM staff
WHERE staff.staff_id IN (
    SELECT works_with.staff_id
    FROM works_with
    WHERE works_with.client_id IN (
        SELECT client.client_id
        FROM client
        WHERE client.branch_id = (
            SELECT branch_id
            FROM branch
            WHERE branch.branch_name = 'Scranton'
        )
    )
);
```
*Output:*
| staff_id | full_name      |
|----------|----------------|
| 102      | Michael Scott  |
| 105      | Stanley Hudson |

<br>

---

**Task: Find the names of all clients who have spent more than 100,000 dollars**
```sql
SELECT client.client_name
FROM client
WHERE client.client_id IN (
    SELECT client_id
    FROM (
        SELECT
            SUM (works_with.total_sales) AS totals,
            client_id
        FROM works_with
        GROUP BY client_id) AS total_client_expenses
    WHERE totals > 100000
);
```
*Output:*
| client_name       |
|-------------------|
| Lackawana Country |
| FedEx             |

<br>

---

## Conclusion

Wow, completing this SQL and database project was no small feat for me! But I'm happy to say that I not only met the expectations for the project, but I tried my best to go above and beyond and create more comprehensive and impressive outputs.

Learning a new skill can be intimidating, but this project helped me feel comfortable and confident in my abilities. It's amazing how far I've come in such a short time, and I'm excited to keep learning and improving.

I'm proud of my work on this project and I hope it shows that I have what it takes to be a data analytics superstar. Bring on the next challenge, because I'm ready to tackle it with enthusiasm and curiosity. Hire me and let's make magic happen with data!

## Contributing

Comments areon how can I improve this is very much welcome, simply message me.

Comments and pull requests are welcome, simply message me. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

Source: [freeCodeCamp.org](https://youtu.be/HXV3zeQKqGY) on Youtube

## Acknowledgments

Thank you to my amazing girlfriend for inspiring me to dream bigger and for always being there for me. You are my rock and I couldn't have done this without you.

Thank you to my supportive family who have always believed in me, even when I didn't believe in myself. Your love and encouragement have been invaluable.

And a big thank you to the internet and chat-gpt for making learning easier and more enjoyable. Without your endless resources and wise words, I would have been lost. You truly are the MVP of my journey.

## Contacts

Reach me out via [email](caynerc@gmail.com) or [LinkedIn](https://www.linkedin.com/in/caynercuritana).