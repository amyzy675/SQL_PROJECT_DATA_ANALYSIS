# 📊 Data Analyst Job Market Analysis with SQL

## Introduction

Dive into the data job market! This project explores:

* 💰 Top-paying data analyst jobs
* 🔥 Most in-demand skills
* 📈 Skills that combine high demand with high salaries

Using SQL, I analyzed real-world job posting data to identify trends that can help aspiring data analysts prioritize skill development and career opportunities.

🔍 SQL queries? Check them out here: [project_sql folder](/project_sql/)

---

## Background

This project was inspired by my desire to better understand the data analyst job market and identify the skills that provide the greatest career value.

The dataset contains information on:

* Job titles
* Salaries
* Job locations
* Required skills
* Companies hiring

### Key Questions

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

---

## 🛠️ Tools Used

### SQL

The primary tool used for querying and analyzing job market data.

### PostgreSQL

Database management system used to store and query the dataset.

### Visual Studio Code

Used for writing and executing SQL queries.

### Git & GitHub

Used for version control and project sharing.

---

# 📈 Analysis

## 1. Top-Paying Data Analyst Jobs

To identify the highest-paying positions, I filtered remote Data Analyst roles with available salary information.

### SQL Query

```sql
SELECT
	job_id,
	job_title,
	job_location,
	job_schedule_type,
	salary_year_avg,
	job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim
	ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst'
    AND job_location = 'Anywhere'
    AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
```

### Key Findings

* Salaries ranged from **$184,000 to $650,000**.
* Companies such as SmartAsset, Meta, and AT&T offered some of the highest-paying opportunities.
* Job titles varied significantly, ranging from Data Analyst positions to Director-level analytics roles.

📊 *Top Paying Roles Bar Chart*

![Top Paying Roles](assets/1_top_paying_roles.png)
*Bar graph visualizing the salary for the top 10 highest-paying Data Analyst jobs; graph generated from SQL query results.*

---

## 2. Skills Required for Top-Paying Jobs

This analysis examined which skills appeared most frequently among the highest-paying data analyst positions.

### SQL Query

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst'
        AND job_location = 'Anywhere'
        AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;
```

### Key Findings

Most common skills among the top-paying jobs:

| Skill   | Count |
| ------- | ----- |
| SQL     | 8     |
| Python  | 7     |
| Tableau | 6     |

Additional frequently requested skills included:

* R
* Snowflake
* Pandas
* Excel

📊 *Top Paying Skills Bar Chart*

![Top Paying Roles Skills](assets/2_top_paying_roles_skills.png)
*Bar graph visualizing the salary for the top 10 highest-paying Data Analyst jobs; graph generated from SQL query results.*

---

## 3. Most In-Demand Skills for Data Analysts

This query identified the skills most frequently requested in remote Data Analyst job postings.

### SQL Query

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```

### Results

| Skill    | Demand Count |
| -------- | ------------ |
| SQL      | 7,291        |
| Excel    | 4,611        |
| Python   | 4,330        |
| Tableau  | 3,745        |
| Power BI | 2,609        |

### Key Findings

* SQL remains the most essential skill for Data Analysts.
* Excel continues to be highly valued despite the rise of programming tools.
* Python, Tableau, and Power BI are increasingly important for data analysis and visualization.

---

## 4. Highest-Paying Skills

This analysis explored which skills are associated with the highest average salaries.

### SQL Query

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25;
```

### Top Paying Skills

| Skill         | Average Salary |
| ------------- | -------------- |
| PySpark       | $208,172       |
| Bitbucket     | $189,155       |
| Couchbase     | $160,515       |
| Watson        | $160,515       |
| DataRobot     | $155,486       |
| GitLab        | $154,500       |
| Swift         | $153,750       |
| Jupyter       | $152,777       |
| Pandas        | $151,821       |
| Elasticsearch | $145,000       |

### Key Findings

* Big Data technologies command premium salaries.
* Machine Learning and data science tools significantly increase earning potential.
* Cloud and data engineering expertise are increasingly valuable.
* Skills bridging analytics and engineering often lead to higher compensation.

---

## 5. Most Optimal Skills to Learn

To determine the best skills to invest time in learning, I combined salary and demand data.

### SQL Query

```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim
    ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim
    ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY skills_dim.skill_id
HAVING COUNT(skills_job_dim.job_id) > 10
ORDER BY avg_salary DESC, demand_count DESC
LIMIT 25;
```

### Top Skills by Demand & Salary

| Skill      | Demand Count | Average Salary |
| ---------- | ------------ | -------------- |
| Go         | 27           | $115,320       |
| Confluence | 11           | $114,210       |
| Hadoop     | 22           | $113,193       |
| Snowflake  | 37           | $112,948       |
| Azure      | 34           | $111,225       |
| BigQuery   | 13           | $109,654       |
| AWS        | 32           | $108,317       |
| Java       | 17           | $106,906       |
| SSIS       | 12           | $106,683       |
| Jira       | 20           | $104,918       |

### Key Findings

#### Programming Languages

* Python and R remain highly valuable due to strong demand.
* Python appeared in 236 postings and R in 148 postings.

#### Cloud & Big Data Technologies

* Snowflake
* Azure
* AWS
* BigQuery

These technologies combine strong demand with above-average salaries.

#### Business Intelligence Tools

* Tableau
* Looker

Visualization skills remain essential for modern analytics roles.

#### Database Technologies

* Oracle
* SQL Server
* NoSQL Databases

Database expertise continues to be a core requirement for data analysts.

---

# 🎯 What I Learned

Throughout this project, I strengthened several key SQL skills:

### 🧩 Advanced SQL Querying

* Complex joins
* Common Table Expressions (CTEs)
* Multi-table analysis

### 📊 Data Aggregation

* GROUP BY
* COUNT()
* AVG()
* Aggregate analysis

### 💡 Analytical Thinking

* Translating business questions into SQL queries
* Extracting actionable insights from real-world datasets

---

# 📌 Conclusions

## Key Insights

### Top-Paying Data Analyst Jobs

Remote Data Analyst roles can reach salaries as high as **$650,000**, demonstrating strong earning potential.

### Skills for Top-Paying Jobs

SQL consistently appears among the highest-paying opportunities, making it one of the most valuable skills for analysts.

### Most In-Demand Skills

SQL is both the most requested and one of the most valuable skills in the job market.

### Highest-Paying Skills

Specialized technologies such as PySpark, DataRobot, and Couchbase command premium salaries.

### Best Skills to Learn

A combination of:

* SQL
* Python
* Cloud Platforms (AWS, Azure, Snowflake)
* Data Visualization Tools (Tableau, Power BI)

offers the strongest balance between market demand and compensation.

---

# 🚀 Closing Thoughts

This project helped me improve my SQL skills while gaining valuable insight into the Data Analyst job market.

The findings provide a roadmap for aspiring data analysts seeking to maximize both employability and earning potential. By focusing on high-demand, high-paying skills, professionals can better position themselves for success in an increasingly competitive analytics landscape.

Continuous learning and adaptation remain essential as new technologies continue to shape the future of data analytics.
