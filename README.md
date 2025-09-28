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

## 📈 Data Analysis & Findings

Here are 15 business questions answered using SQL queries.

#### 1. Count the number of Movies vs. TV Shows
* **Question:** What is the distribution of content types on Netflix?
* **Query:**
    ```sql
    SELECT 
        type,
        COUNT(*) AS total_count
    FROM netflix
    GROUP BY type;
    ```
* **Insight:** Netflix has a significantly larger library of movies compared to TV shows. This could inform acquisition strategy, focusing on either balancing the two or further strengthening the movie catalog.

#### 2. Find the most common rating
* **Question:** What are the most frequent content ratings for movies and TV shows?
* **Query:**
    ```sql
    WITH RatingCounts AS (
        SELECT 
            type,
            rating,
            COUNT(*) AS rating_count,
            RANK() OVER (PARTITION BY type ORDER BY COUNT(*) DESC) AS rnk
        FROM netflix
        WHERE rating IS NOT NULL
        GROUP BY type, rating
    )
    SELECT 
        type,
        rating AS most_frequent_rating,
        rating_count
    FROM RatingCounts
    WHERE rnk = 1;
    ```
* **Insight:** The most common rating for movies is `TV-MA` (Mature Audiences), while for TV shows, it's `TV-14` (Parents Strongly Cautioned). This suggests Netflix's content largely targets adult and young adult audiences.

#### 3. List all movies released in 2020
* **Question:** Can we get a list of all content released in the year 2020?
* **Query:**
    ```sql
    SELECT title, type, release_year
    FROM netflix
    WHERE release_year = 2020;
    ```
* **Insight:** This query allows for a quick snapshot of content from a specific year, useful for analyzing trends or a year's performance.

#### 4. Find the top 5 countries with the most content
* **Question:** Which countries are the top producers of content available on Netflix?
* **Query:**
    ```sql
    SELECT 
        country,
        COUNT(*) as total_content
    FROM (
        SELECT TRIM(UNNEST(STRING_TO_ARRAY(country, ','))) AS country
        FROM netflix
    ) AS countries
    WHERE country IS NOT NULL AND country != ''
    GROUP BY country
    ORDER BY total_content DESC
    LIMIT 5;
    ```
* **Insight:** The United States is the largest content contributor by a wide margin, followed by India and the United Kingdom. This highlights key markets for content production and acquisition.

#### 5. Identify the longest movie
* **Question:** What is the longest movie available on Netflix by duration?
* **Query:**
    ```sql
    SELECT 
        title,
        duration
    FROM netflix
    WHERE type = 'Movie' AND duration IS NOT NULL
    ORDER BY CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) DESC
    LIMIT 5;
    ```
* **Insight:** This helps identify outliers in content length. The longest movies are often documentaries or special features.

#### 6. Find content added in the last 5 years
* **Question:** What content has been added to Netflix recently (within the last 5 years)?
* **Query:**
    ```sql
    -- Note: This query uses the current date and might need a fixed date for reproducibility.
    -- The TO_DATE function is specific to PostgreSQL.
    SELECT
        title,
        date_added
    FROM netflix
    WHERE TO_DATE(date_added, 'FMMonth FMDD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
    ```
* **Insight:** Analyzing recently added content helps understand Netflix's current acquisition trends and freshness of the library.

#### 7. Find all content directed by 'Rajiv Chilaka'
* **Question:** Can we list all the works of a specific director, for example, Rajiv Chilaka?
* **Query:**
    ```sql
    SELECT title, type, country
    FROM netflix
    WHERE director LIKE '%Rajiv Chilaka%';
    ```
* **Insight:** This query is useful for talent analysis, allowing stakeholders to see the entire portfolio of a specific director on the platform. Rajiv Chilaka is a prominent director of Indian children's content.

#### 8. List all TV shows with more than 5 seasons
* **Question:** Which TV shows are long-running (more than 5 seasons)?
* **Query:**
    ```sql
    SELECT 
        title,
        duration
    FROM netflix
    WHERE 
        type = 'TV Show'
        AND CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) > 5
    ORDER BY CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) DESC;
    ```
* **Insight:** Long-running TV shows often indicate high viewer engagement and loyalty. These are valuable assets for the platform.

#### 9. Count the number of content items in each genre
* **Question:** What are the most popular genres on Netflix?
* **Query:**
    ```sql
    SELECT 
        genre,
        COUNT(*) as total_content
    FROM (
        SELECT TRIM(UNNEST(STRING_TO_ARRAY(listed_in, ','))) AS genre
        FROM netflix
    ) AS genres
    GROUP BY genre
    ORDER BY total_content DESC
    LIMIT 10;
    ```
* **Insight:** "International Movies," "Dramas," and "Comedies" are the most dominant genres. This data is crucial for content acquisition and scheduling.

#### 10. Find the top 5 years with the highest percentage of content releases from India
* **Question:** In which years did India produce the most content that ended up on Netflix?
* **Query:**
    ```sql
    SELECT 
        release_year,
        COUNT(*) AS total_releases,
        ROUND(
            COUNT(*)::NUMERIC * 100 / (SELECT COUNT(*) FROM netflix WHERE country LIKE '%India%'), 2
        ) AS percentage_of_total
    FROM netflix
    WHERE country LIKE '%India%'
    GROUP BY release_year
    ORDER BY total_releases DESC 
    LIMIT 5;
    ```
* **Insight:** The years 2018, 2017, 2019, 2020, and 2016 saw the highest output of Indian content. This indicates a recent surge in production from this region.

#### 11. List all movies that are documentaries
* **Question:** Can we filter the library to show only documentaries?
* **Query:**
    ```sql
    SELECT title, release_year
    FROM netflix
    WHERE listed_in LIKE '%Documentaries%';
    ```
* **Insight:** A simple but effective query to segment the catalog for a specific genre, which can be used for creating curated collections.

#### 12. Find all content without a listed director
* **Question:** How much of the content is missing director information?
* **Query:**
    ```sql
    SELECT show_id, title, type
    FROM netflix
    WHERE director IS NULL;
    ```
* **Insight:** A significant portion of the content, especially TV shows and non-feature films, lacks director data. This highlights a data quality issue that could be addressed.

#### 13. Find movies starring 'Salman Khan' released in the last 10 years
* **Question:** How many recent movies featuring a specific major actor (e.g., Salman Khan) are on Netflix?
* **Query:**
    ```sql
    SELECT title, release_year
    FROM netflix
    WHERE 
        casts LIKE '%Salman Khan%'
        AND type = 'Movie'
        AND release_year >= (EXTRACT(YEAR FROM CURRENT_DATE) - 9);
    ```
* **Insight:** This helps in analyzing the presence of popular actors, which can be a major draw for subscribers in specific regions like India.

#### 14. Find the top 10 actors in Indian movies
* **Question:** Who are the most frequently cast actors in Indian content on Netflix?
* **Query:**
    ```sql
    SELECT 
        actor,
        COUNT(*) AS movie_count
    FROM (
        SELECT TRIM(UNNEST(STRING_TO_ARRAY(casts, ','))) AS actor
        FROM netflix
        WHERE country LIKE '%India%' AND type = 'Movie'
    ) AS actors
    WHERE actor != 'No Cast' AND actor IS NOT NULL
    GROUP BY actor
    ORDER BY movie_count DESC
    LIMIT 10;
    ```
* **Insight:** Anupam Kher, Shah Rukh Khan, and Naseeruddin Shah are among the most prolific actors in the Indian movie catalog on Netflix. Partnering with such actors could be a strategic move.

#### 15. Categorize content based on description keywords
* **Question:** Can we categorize content based on potentially sensitive keywords like 'kill' or 'violence' in their descriptions?
* **Query:**
    ```sql
    SELECT 
        category,
        type,
        COUNT(*) AS content_count
    FROM (
        SELECT 
            *,
            CASE 
                WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Contains Sensitive Keywords'
                ELSE 'Does Not Contain Sensitive Keywords'
            END AS category
        FROM netflix
    ) AS categorized_content
    GROUP BY category, type
    ORDER BY type, category;
    ```
* **Insight:** This query provides a high-level content audit. A small fraction of the descriptions contains potentially sensitive keywords, which can be useful for content rating verification and parental control features.

---

## ✅ Conclusion

This project successfully demonstrates the power of SQL for conducting in-depth data analysis on a real-world dataset. By writing queries to address specific business questions, we were able to extract valuable insights into Netflix's content strategy, popular genres, key markets, and top talent. The analysis showcases skills in data aggregation, string manipulation, subqueries, and conditional logic, providing a solid foundation for data-driven decision-making in the media industry.

---

## 🚀 How to Use

1.  **Clone the Repository**:
    ```bash
    git clone [https://github.com/your-username/Netflix-SQL-Analysis.git](https://github.com/your-username/Netflix-SQL-Analysis.git)
    cd Netflix-SQL-Analysis
    ```

2.  **Database Setup**:
    * Set up a PostgreSQL database.
    * Create the `netflix` table using the schema provided in the project `README`.
    * Import the Netflix dataset (e.g., from a `.csv` file) into the `netflix` table.

3.  **Run Queries**:
    * Open the `netflix_analysis.sql` file in your preferred SQL client (like DBeaver, pgAdmin, or VS Code).
    * Run the queries individually to reproduce the analysis and findings.

---

## 👨‍💻 Author

**Deewakar Kumar**  
📍 Bokaro, Jharkhand, India  
📧 Email: deewakar2412@gmail.com  
🔗 [LinkedIn](https://www.linkedin.com/in/deewakar-kumar-b61652248/)  

---

## 📜 License

This project is licensed under the MIT License.  
Feel free to use, share, and modify for learning and development purposes.

