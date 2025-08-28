# Project Budget and Employee Performance Analysis
This repository contains the resources and insights related to analyzing project budgets and employee performance to identify areas at risk of being over budget or underperforming. The analysis aims to provide a clear overview of financial health and operational efficiency within various departments and projects, with a specific focus on whether annual budgets can cover expenses, given that department budgets are set at two-year intervals.

## Project Overview
In many organizations, project and department management face challenges in staying within budget and maintaining optimal performance. 
This project addresses these challenges by providing a structured approach to:

Identifying departments and projects that are "in the RED" (over budget or underperforming).
Ensuring robust data organization for accurate reporting.
Developing an interactive Power BI dashboard for real-time visibility.

A critical aspect of this analysis is understanding if a single year's budget allocation is sufficient to cover all expenses, especially since department budgets are set for two-year intervals.

## Key Objectives
The primary goals of this project are:

1. **Identify At-Risk Areas:** Pinpoint specific departments and projects that are either exceeding their allocated budgets or failing to meet performance              expectations.

2. **Financial Viability Assessment:** Determine if the annual portion of a two-year department budget is adequate to cover operational and project-related            expenses.

3. **Data Structure and Accessibility:** Establish a well-organized and accessible data infrastructure, integrating employee information, salary data, department      budgets, and project details.

4. **Interactive Reporting:** Create a comprehensive Power BI dashboard to visualize:

      Employee performance metrics.

      Salary distribution across departments.

      Department and project budget management status.
   
## Data Organization
To ensure accurate and accessible reporting, data from various sources is structured as follows:

1. **Employee Information:** Details about individual employees (e.g.```employee_id``` , ```first_name``` , ```last_name``` , ```email``` , ```job_title``` ,
   ```salary``` , ```hire_date```).

3. **Salary Data:** Specific salary figures and related compensation details.

4. **Department Budgets:** Financial allocations for each department, noting the two-year interval for budget setting (e.g.```Department_ID``` , ```Department_Name```, ```Department_Budget``` , ```Head_of_Department``` , ```Number_of_Employees``` , ```Department_Goals``` , ```Location```).

5. **Project Details:** Information regarding various projects, including budgets, timelines, and assigned personnel (e.g. ```project_id``` , ```project_name``` , ```project_budget```, ```project_start_date``` , ```project_end_date``` , ```department_id``` , ```project_lead```).

6. **Project Assignments:** Links employees to the projects they are working on.

## Power BI Dashboard
The core output of this project is an interactive Power BI dashboard designed to provide comprehensive visibility into the key areas of analysis. The dashboard includes:

1. **Employee Overview:** Displays individual employee details, including their headshot (imported from a .CSV file), name, job title, department, and salary.

2. **Budget vs. Performance Summary:** Visualizations showing the sum of project costs by department, capital (to indicate over-budget departments), and overall budget and salary costs.

3. **Departmental Analysis:** Breakdown of department names, goals, project costs, salary costs, and calculated "Capital" to highlight departments over budget. The capital column is calculated as: ```[Budget] * 0.5 - ([Salary cost] * 2 + [Project cost])```, where ```[Budget]``` represents the 2-year department budget.    This formula assesses if one year's allocated budget can cover the expenses.

4. **Project Budget Overview:** Bar charts illustrating individual project budgets for both upcoming and completed projects.

The dashboard allows for filtering and slicing data by ```Department_Name``` and ```Status``` (Upcoming/Completed) to drill down into specific areas of interest.

## SQL Queries
The following SQL queries were used to extract, join, and transform the data from the underlying database for analysis:

**1. Retrieve Employee and Department Data**
```sql
SELECT * FROM employees;
SELECT * FROM departments;
```
**2. Join Employees and Departments**
```sql   
SELECT *
FROM employees AS e
JOIN departments AS d
ON e.department_id = d.Department_ID;
```
**3. Join Employees, Departments, and Project Assignments**
 ```sql   
SELECT
    e.employee_id,
    e.first_name,
    e.last_name,
    e.job_title,
    e.salary,
    d.Department_Name,
    pa.project_id
FROM employees AS e
JOIN departments AS d
ON e.department_id = d.Department_ID
JOIN project_assignments AS pa
ON pa.employee_id = e.employee_id;
```
**4. Retrieve Upcoming and Completed Projects**
```sql 
SELECT * FROM [upcoming projects];
SELECT * FROM completed_projects;
```
**5. Union of Project Budgets**
```sql    
SELECT
    project_id,
    project_name,
    project_budget
FROM [upcoming projects]

UNION

SELECT
    project_id,
    project_name,
    project_budget
FROM completed_projects;
```
**6. Union of Project Status with 'UPCOMING'/'COMPLETED' status**
 ```sql   
SELECT
    project_id,
    project_name,
    project_budget,
    'UPCOMING' as status
FROM [upcoming projects]

UNION all

SELECT
    project_id,
    project_name,
    project_budget,
    'COMPLETED' as status
FROM completed_projects;
```
**7. Comprehensive Data Join for Power BI (including Project Status)**
 ```sql   
-- project status
WITH project_status AS (
    SELECT
        project_id,
        project_name,
        project_budget,
        'UPCOMING' as status
    FROM [upcoming projects]

    UNION all

    SELECT
        project_id,
        project_name,
        project_budget,
        'COMPLETED' as status
    FROM completed_projects
)

-- main table
SELECT
    e.employee_id,
    e.first_name,
    e.last_name,
    e.job_title,
    e.salary,
    d.Department_Name,
    d.Department_Budget,
    d.Department_Goals,
    pa.project_id,
    p.project_name,
    p.project_budget,
    p.status
FROM employees AS e
JOIN departments AS d
ON e.department_id = d.Department_ID
JOIN project_assignments AS pa
ON pa.employee_id = e.employee_id
JOIN project_status AS p
ON p.project_id = pa.project_id;
```
## Setup and Usage
To replicate this analysis and view the dashboard, you will need:

1. **SQL Server Database:** A database containing the ```employees``` , ```departments``` , ```project_assignments``` , ```upcoming_projects``` , and ```completed_projects``` tables.

2. **Power BI Desktop:** To connect to your database, import the data, and view the dashboard.

## Steps:
1. **Database Connection:**

   Open Power BI Desktop.

   Select "Get data" -> "SQL Server database".

   Enter your SQL Server details (Server name and optional Database name).

   In the "SQL statement (optional, requires database)" field, paste the comprehensive SQL query from section 7 above.

   Choose "Import" for Data Connectivity mode.

2. **Import Employee Headshots:**

   If you have a CSV file with ```Employee_ID``` and ```Head_Shot``` URLs, import it into Power BI.

   Ensure the ```Head_Shot``` column is categorized as "Image URL" in Power BI's Data View.

3. **Create Custom Column (Capital):**

   In Power BI's Query Editor, navigate to "Add Column" -> "Custom Column".

   Name the new column ```Capital```.

   Use the formula: ```[Budget] * 0.5 - ([Salary cost] * 2 + [Project cost])``` (adjust column names as per your dataset). This formula assumes ```Budget```          refers to the two- year department budget, and we are assessing if half of that budget (one year's worth) can cover the combined ```Salary cost``` (doubled to     reflect a 2-year interval for comparison with the 2-year budget) and ```Project cost```.

4. **Dashboard Visualization:** Arrange and configure the visuals as depicted in the provided documentation to create the interactive dashboard.

