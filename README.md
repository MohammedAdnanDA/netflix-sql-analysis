# 📺 Netflix Data Analysis Project using SQL

## 🧠 Project Overview

This project involves performing SQL queries on the Netflix dataset to derive insights and explore the content available on the platform. The goal is to practice real-world data analysis using structured queries and extract valuable information from the Netflix content catalog.

## 📦 Dataset Source

- **Source**: [Kaggle - Netflix Movies and TV Shows](https://www.kaggle.com/datasets/shivamb/netflix-shows)
- **Rows**: ~8800+
- **Description**: The dataset contains information about movies and TV shows available on Netflix as of 2021.

## 🧾 Table Schema

The dataset contains the following fields:

- **show_id**: Unique ID for every Movie / TV Show  
- **type**: Type of content - Movie or TV Show  
- **title**: Title of the Movie / TV Show  
- **director**: Director of the content  
- **cast**: Actors involved  
- **country**: Country of production  
- **date_added**: Date it was added to Netflix  
- **release_year**: Year of release  
- **rating**: Age rating  
- **duration**: Duration of the content  
- **listed_in**: Genre  
- **description**: Short description of the Movie / TV Show  

```sql
CREATE TABLE netflix (
    show_id VARCHAR(6),
    type VARCHAR(10),
    title VARCHAR(150),
    director VARCHAR(208),
    cast VARCHAR(1000),
    country VARCHAR(150),
    date_added VARCHAR(50),
    release_year INT,
    rating VARCHAR(10),
    duration VARCHAR(15),
    listed_in VARCHAR(255),
    description VARCHAR(250)
);
```

---

## 🔍 SQL Insights & Queries

### 1️⃣ Count the number of Movies vs TV Shows
```sql
SELECT type, COUNT(*) AS count
FROM netflix
GROUP BY type;
```

### 2️⃣ Most common rating for Movies and TV Shows
```sql
SELECT type, rating, COUNT(*) AS count
FROM netflix
GROUP BY type, rating
ORDER BY type, count DESC;
```

### 3️⃣ List all movies released in a specific year (e.g., 2020)
```sql
SELECT title, release_year
FROM netflix
WHERE type = 'Movie' AND release_year = 2020;
```

### 4️⃣ Top 5 countries with the most content on Netflix
```sql
SELECT country, COUNT(*) AS total_content
FROM netflix
WHERE country IS NOT NULL
GROUP BY country
ORDER BY total_content DESC
LIMIT 5;
```

### 5️⃣ Identify the longest movie
```sql
SELECT title, duration
FROM netflix
WHERE type = 'Movie'
ORDER BY CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) DESC
LIMIT 1;
```

### 6️⃣ Content added in the last 5 years
```sql
SELECT title, date_added
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= (CURRENT_DATE - INTERVAL '5 years');
```

### 7️⃣ All movies/TV shows by director 'Rajiv Chilaka'
```sql
SELECT *
FROM netflix
WHERE director = 'Rajiv Chilaka';
```

### 8️⃣ TV shows with more than 5 seasons
```sql
SELECT title, duration
FROM netflix
WHERE type = 'TV Show'
  AND CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) > 5;
```

### 9️⃣ Number of content items in each genre
```sql
SELECT UNNEST(STRING_TO_ARRAY(listed_in,',')) AS genre, COUNT(*) AS total
FROM netflix
GROUP BY genre
ORDER BY total DESC;
```

### 🔟 Top 5 years with highest average content release in India
```sql
SELECT release_year, AVG(release_count) AS avg_release_per_year
FROM (
    SELECT release_year, COUNT(*) AS release_count
    FROM netflix
    WHERE country = 'India'
    GROUP BY release_year
) AS year_counts
GROUP BY release_year
ORDER BY avg_release_per_year DESC
LIMIT 5;
```

### 1️⃣1️⃣ All movies that are documentaries
```sql
SELECT title
FROM netflix
WHERE type = 'Movie' AND listed_in ILIKE '%Documentaries%';
```

### 1️⃣2️⃣ Content without a director
```sql
SELECT title
FROM netflix
WHERE director IS NULL OR TRIM(director) = '';
```

### 1️⃣3️⃣ Number of movies with 'Salman Khan' in last 10 years
```sql
SELECT COUNT(*) AS total, title
FROM netflix
WHERE type = 'Movie'
  AND cast ILIKE '%Salman Khan%'
  AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10
GROUP BY title;
```

### 1️⃣4️⃣ Top 10 actors in Indian movies who have appeared in the highest number of movies
```sql
WITH actor_list AS (
    SELECT UNNEST(STRING_TO_ARRAY(cast, ', ')) AS actor
    FROM netflix
    WHERE type = 'Movie' AND country LIKE '%India%' AND cast IS NOT NULL
)
SELECT actor, COUNT(*) AS movie_count
FROM actor_list
GROUP BY actor
ORDER BY movie_count DESC
LIMIT 10;
```

### 1️⃣5️⃣ Categorize content based on presence of 'kill' or 'violence' in description as 'Bad' or 'Good'
```sql
SELECT 
  CASE 
    WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
    ELSE 'Good'
  END AS category,
  COUNT(*) AS total
FROM netflix
GROUP BY category;
```

---

## 🚀 Conclusion

This project demonstrates how SQL can be effectively used to explore and analyze large-scale streaming data. The queries help uncover valuable patterns and content insights from Netflix’s massive library, making it an ideal practice ground for aspiring data analysts.
