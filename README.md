# Introduction

Dive into the data job market! Focusing on data analyst roles, this project explores top-paying jobs, in-demand skills, and where high demand meets high salary in data analytics.

SQL queries? Check them out here: (https://github.com/benhorwitz04/SQL_Project_Data_Job_Analysis_Project/tree/main/.vscode/project_sql)

# Background

Driven by a quest to naviagte the data analyst jonb market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

Data hails from the SQL course (https://lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations, and essential skills.
# The questions I wanted to answer through my SQL queries were: 

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs>
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I used

For my deep dive into the data analyst job market, I harnassed the power of several key tools:

- **SQL:** The backbone of my analysis, allow me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code (VS Code):** My go-to for database management and executing SQL queries.
- **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis #

Each query for this project aimed at investigating specific aspects of the data analyst job market. Here's how I approached each question:

### 1. Top Paying Data Analyst Jobs

To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focsuing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10
```

![5f520304-d752-47b6-ab4a-205e8b1dccf1](https://github.com/user-attachments/assets/f2ff8771-52e9-4fe2-b965-fd2abf4fc8fa)

*Bar graph visualizing the slary for the top 10 salaries for data analysts; ChatGPT generated this graph from SQL query results.*

Heres the breakdown of the top data analyst jobs in 2023:
- **Wide Salary Range:** Top 10 paying data analyst roles span from $184,000 to $650,000, indicating significant salary potential in the field.
- **Diverse Enloyers:** Companies like SmartAsset, Meta, and AT&T are among those offering high salaries, showing a broad interest across different industries.
- **Job Title Variety:** There's a hgih diversity in job titles, from Data Analyst to Director of Analytics, reflecting varied roles and specializations within data analytics.

### 2. Skills for Top-Paying Jobs

To understand what skills are requried for top-paying jobs, I joined the job postings with the skills data, providing insights into what employees value for high-compensation roles.

```sql
WITH top_paying_jobs AS (

    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short = 'Data Analyst' AND
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
    )

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC
```

Here's the breakdown of the most demanded skills for the top 10 highest paying data analyst jobs in 2023:

* **SQL** is leading with a bold count of 8
* **Python** follows closely with a bold count of 7
* **Tableau** is also highly sought after, with a bold count of 6. Other skills like **R**, **Snowflake**, **Pandas** and **Excel** show varying degrees of demand.

![2_top_10_paying_jobs_skills_chart](https://github.com/user-attachments/assets/bbd7fbbd-f11e-4032-bf30-fab3aa54ffb8)

*Bar graph visualizing the count of skills for the top 10 paying jobs for data analysts; the chart was generated on Excel from my SQL query results.*

### 3. In-Demand Skills for Data Analysts

This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills,
    COUNT (skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5
```
Here's the breakdown of the most demanded skils for data analysts in 2023
* **SQL** and **Excel** remain fundamental, emphasizing the need for strong foundational skills in data processing and spreadsheet manipulation.
* Programming and Visualization Tools like Python, Tableau, and Power BI are essential, pointing towards increasing importance of technical skills in data storytelling and decision support.

| Skills     | Demand Count |
|------------|--------------|
| SQL        | 7291         |
| Excel      | 4611         |
| Python     | 2609         |
| Tableau    | 3745         |
| Power BI   | 2609         |

*Table of the demand for the top 5 skills in data analyst postings*

### 4. Skills Based on Salary

Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql
SELECT
    skills,
    ROUND (AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25
```

Here's a breakdown of the results for top paying skills for Data Analysts:
* **High Demand for Big Data and ML Skills:** Top salaries are commanded by analyst skilled in big data technologies (PySpark, Couchbase), machine learning tools (DataRobot, Jupyter), and in Python libraries (Pandas, NumPy), reflecting the industry's high evaluation of data processing and predictive modeling capabilities.
* **Software Development & Deployment Proficiency:** Knowledge and development and deployment tools (GitLab, Kubernetes, Airflow) indicates a lucrative crossover between data analysis and engineering with the premium on skills that facilitate automation and deficient data pipeline management.
* **Cloud Computing Expertise:** Familiarity with cloud and data engineering tools (Elasticsearch, Databricks, GCP) underscores the growing importance of cloud-based analytics environment, suggesting that cloud proficiency significantly boosts earning potential and data analytics.


| Skills     | Average Salary ($) |
|------------|--------------|
| pyspark    | 208,172      |
| butbucket  | 189,155      |
| couchbase  | 160,515      |
| watson     | 160,515      |
| datarobot  | 155,486      |
| gitlab     | 154,500      |
| swift      | 153,750      |
| jupyter    | 152,777      |
| pandas     | 151,821      |
| electricsearch |145,00    |

*Table of the average salary for the top 10 paying skills for data analysts*

### 5. Most Optimal Skills to Learn

Combining the insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skilled development.

```sql
SELECT
    sd.skill_id,
    sd.skills,
    COUNT(sjd.job_id) AS demand_count,
    ROUND(AVG(j.salary_year_avg), 0) AS avg_salary
FROM job_postings_fact j
JOIN skills_job_dim sjd ON j.job_id = sjd.job_id
JOIN skills_dim sd ON sjd.skill_id = sd.skill_id
WHERE
    j.job_title_short = 'Data Analyst'
    AND j.salary_year_avg IS NOT NULL
    AND j.job_work_from_home = TRUE
GROUP BY
    sd.skill_id, sd.skills
HAVING COUNT(sjd.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
```
| Skill ID  | Skills | Demand Count | Average Salary|
|------------|--------------|--------------|--------------|
|8   | go   | 27  | 115,320 |
| 234  | cofluence  | 11 | 114,210 |
| 97  | hadoop   | 22 | 113,193 |
|80   | snowflake | 37 | 112,948 |
| 74| azure  | 34 | 111,225 |
| 77 | bigquery | 13 | 109,654 |
| 76  | aws | 32 | 108,317 |
|4 | java | 17 | 106,906 |
|194| ssis | 12 | 106,683 |
|233 |jira | 20 | 104,918 |

*Table of the most optimal  skills for data analyst sorted by slaary*

Here's a breakdown of the most optimal skills for data analyst in 2023:

* **High-Demand Programming Languages:** Python, and R stand out for their high demand, with demand counts of 236 and 148 respectively. Despite their high demand, their average salaries are around $101,397 for Python and $100,499 for R, indicating that proficiency in these languages is highly valued but also widely available.
* **Cloud Tools and Technologies:** Skills in specialized technologies such a Snowflake, Azure, AWS, and BigQuery show significant demand with relatively highest average salaries, pointing towards the growing importance of cloud computing platforms and big data technologies in data analysis.
* **Business Intelligence and Visualization Tools:** Tableau and Looker, with the demand counts of 230 and 49 respectively, and average salaries around $99,288 and $103,795, highlight the critical role of data visualization and business intelligence and delivering actual insights from data.
* **Database Technologies:** The demand for skills in traditional and NoSQL databases (Oracle, SQL Sever, NoSQL) with average salaries among ranging from $97,786 to $104,534, reflects the enduring need for data storage, retrieval, and management expertise.

# What I learned 

Through this adventure, I've turbocharge my SQL toolkit with some serious fire-power:

* **Complex Query Crafting:** Mastered the art of advanced SQL, emerging tables like a pro in wielding WITJ clauses for ninja-level temp table maneuvers.
* **Data Aggregation:** Got cozy with group by and aggregate functions like COUNT () and AVG () into my data summarizing sidekicks.
* **Analytical Wizardry:** Leveled up my real-world, puzzling-solving skills, turning questions into actionable, insightful SQL queries.

# Conclusions

### Insights
1. **Top-Paying Data Analyst Jobs:** the highest paying jobs for data analyst that lab remote work offer a wide range of salaries, the highest at $650,000!
2. **Skills for Top-Paying Jobs:** High paying data analyst jobs required advanced proficiency in SQL, suggesting it's a critical skill for earning a top salary.
3. **Most In-Demand Skills:** SQL is also the most demanded skill in the data analyst job market, thus making it essential for job seekers.
4. **Skills with Higher Salaries:** Specialized skills, such as SVN and Solidity, are associated with the highest average salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value:** SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analyst to learn to maximize their market value.

### Closing Thoughts

This project was very helpful for understanding what exactly SQL is, and the role it plays in the careers of data analysts. The instructor did a great job of teaching SQL, how to write code, explaining certain functions, operators, and applications/software used to query data. As someone who is starting to take an interest in data analytics, and is considering a career change into the field, this was a very insightful project to work on. I'm already excited to take some of the skills, continue to rehearse and build on them, and use them for projects of my own interest!
