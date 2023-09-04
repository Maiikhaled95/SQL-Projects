**Schema (PostgreSQL v15)**

    -- Create 'departments' table
    CREATE TABLE departments (
        id SERIAL PRIMARY KEY,
        name VARCHAR(50),
        manager_id INT
    );
    
    -- Create 'employees' table
    CREATE TABLE employees (
        id SERIAL PRIMARY KEY,
        name VARCHAR(50),
        hire_date DATE,
        job_title VARCHAR(50),
        department_id INT REFERENCES departments(id)
    );
    
    -- Create 'projects' table
    CREATE TABLE projects (
        id SERIAL PRIMARY KEY,
        name VARCHAR(50),
        start_date DATE,
        end_date DATE,
        department_id INT REFERENCES departments(id)
    );
    
    -- Insert data into 'departments'
    INSERT INTO departments (name, manager_id)
    VALUES ('HR', 1), ('IT', 2), ('Sales', 3);
    
    -- Insert data into 'employees'
    INSERT INTO employees (name, hire_date, job_title, department_id)
    VALUES ('John Doe', '2018-06-20', 'HR Manager', 1),
           ('Jane Smith', '2019-07-15', 'IT Manager', 2),
           ('Alice Johnson', '2020-01-10', 'Sales Manager', 3),
           ('Bob Miller', '2021-04-30', 'HR Associate', 1),
           ('Charlie Brown', '2022-10-01', 'IT Associate', 2),
           ('Dave Davis', '2023-03-15', 'Sales Associate', 3);
    
    -- Insert data into 'projects'
    INSERT INTO projects (name, start_date, end_date, department_id)
    VALUES ('HR Project 1', '2023-01-01', '2023-06-30', 1),
           ('IT Project 1', '2023-02-01', '2023-07-31', 2),
           ('Sales Project 1', '2023-03-01', '2023-08-31', 3);
           
           UPDATE departments
    SET manager_id = (SELECT id FROM employees WHERE name = 'John Doe')
    WHERE name = 'HR';
    
    UPDATE departments
    SET manager_id = (SELECT id FROM employees WHERE name = 'Jane Smith')
    WHERE name = 'IT';
    
    UPDATE departments
    SET manager_id = (SELECT id FROM employees WHERE name = 'Alice Johnson')
    WHERE name = 'Sales';
---
**Query #1** **Find the longest ongoing project for each department.**

    SELECT name,MAX(end_date - start_date) AS duration
    FROM projects
    GROUP BY name
    ORDER BY duration DESC
    LIMIT 1;

| name            | duration |
| --------------- | -------- |
| Sales Project 1 | 183      |

---
**Query #2** **Find all employees who are not managers.**

    SELECT name, job_title
    FROM employees
    WHERE job_title NOT LIKE '%Manager%';

| name          | job_title       |
| ------------- | --------------- |
| Bob Miller    | HR Associate    |
| Charlie Brown | IT Associate    |
| Dave Davis    | Sales Associate |

---
**Query #3** **Find all employees who have been hired after the start of a project in their department.**

    SELECT e.name, e.hire_date, p.start_date
    FROM employees AS e
    INNER JOIN projects AS p
    ON e.department_id = p.department_id
    WHERE e.hire_date > p.start_date;

| name       | hire_date                | start_date               |
| ---------- | ------------------------ | ------------------------ |
| Dave Davis | 2023-03-15T00:00:00.000Z | 2023-03-01T00:00:00.000Z |

---
**Query #4** **Rank employees within each department based on their hire date (earliest hire gets the highest rank).**

    SELECT name,
    RANK() OVER(ORDER BY hire_date ASC)
    FROM employees;

| name          | rank |
| ------------- | ---- |
| John Doe      | 1    |
| Jane Smith    | 2    |
| Alice Johnson | 3    |
| Bob Miller    | 4    |
| Charlie Brown | 5    |
| Dave Davis    | 6    |

---
**Query #5** **Find the duration between the hire date of each employee and the hire date of the next employee hired in the same department.**

    SELECT d.name, e2.hire_date - e1.hire_date AS duration
    FROM employees AS e1
    INNER JOIN employees AS e2
    ON e1.department_id = e2.department_id
    INNER JOIN departments AS d
    ON e1.department_id = d.id 
    WHERE e2.hire_date > e1.hire_date;

| name  | duration |
| ----- | -------- |
| HR    | 1045     |
| IT    | 1174     |
| Sales | 1160     |

---
