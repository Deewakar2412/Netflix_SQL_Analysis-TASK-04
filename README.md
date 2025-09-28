# 🎬 Netflix Data Analysis (SQL)

![SQL](https://img.shields.io/badge/SQL-Analysis-blue) ![Intermediate](https://img.shields.io/badge/Level-Intermediate-green) ![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 📑 Table of Contents

* [Project Overview](#-project-overview)  
* [Objectives](#-objectives)  
* [Project Structure](#-project-structure)  
* [Data Analysis & Queries](#-data-analysis--queries)  
* [Findings](#-findings)  
* [Reports](#-reports)  
* [Screenshots](#-screenshots)  
* [Conclusion](#-conclusion)  
* [How to Use](#-how-to-use)  
* [Author](#-author)  
* [License](#-license)  

---

## 📌 Project Overview

**Project Title:** Netflix Data Analysis  
**Level:** Intermediate  
**Database:** `Netflix`  

This project demonstrates **SQL data analysis techniques** on a Netflix dataset.  
It involves querying, cleaning, and analyzing Netflix content to extract **business insights**,  
such as most popular genres, content distribution across countries, and actor/director analysis.

---

## 🎯 Objectives

1. **Database Setup:** Create and populate the Netflix database.  
2. **Data Cleaning:** Handle missing/null values (e.g., missing director info).  
3. **Exploratory Analysis:** Analyze ratings, duration, release years.  
4. **Business Analysis:** Solve real-world business problems using SQL queries.  

---

## 🗂️ Project Structure

Netflix_SQL_Analysis/
│── README.md
│── netflix_analysis.sql
│── screenshots/


---

## 🧮 Data Analysis & Queries

### Database Setup

```sql
DROP TABLE IF EXISTS netflix;

CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);

SELECT * FROM netflix;

Business Analysis Queries

1. Count Movies vs TV Shows
SELECT type, COUNT(*) 
FROM netflix
GROUP BY type;
2. Most common rating for each type
WITH RatingCounts AS (
    SELECT type, rating, COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT type, rating, rating_count,
           RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT type, rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
