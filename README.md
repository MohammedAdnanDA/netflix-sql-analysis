# 📺 Netflix Data Analysis Project using SQL

This project involves performing SQL queries on the Netflix dataset to derive insights and explore the content available on the platform. The dataset contains information about movies and TV shows available on Netflix as of 2021.

## 🧾 Schema

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

---

## 📌 SQL Queries and Analysis

> 🔽 Below are the queries executed on the dataset to derive different insights:

---

```sql
-- 1️⃣ Count the number of Movies vs TV Shows
SELECT type, COUNT(*) AS count
FROM netflix
GROUP BY type;

-- 2️⃣ Most common rating for Movies and TV Shows
SELECT type, rating, COUNT(*) AS count
FROM netflix
GROUP BY type, rating
ORDER BY type, count DESC;

-- 3️⃣ List all movies released in a specific year (e.g., 2020)
SELECT title, release_year
FROM netflix
WHERE type = 'Movie' AND release_year = 2020;

-- 4️⃣ Top 5 countries with the most content on Netflix
SELECT country, COUNT(*) AS total_content
FROM netflix
WHERE country IS NOT NULL
GROUP BY country
ORDER BY total_content DESC
LIMIT 5;

-- 5️⃣ Identify the longest movie
SELECT title, duration
FROM netflix
WHERE type = 'Movie'
ORDER BY CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) DESC
LIMIT 1;

-- 6️⃣ Content added in the last 5 years
SELECT title, date_added
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= (CURRENT_DATE - INTERVAL '5 years');

-- 7️⃣ All movies/TV shows by director 'Rajiv Chilaka'
SELECT *
FROM netflix
WHERE director = 'Rajiv Chilaka';

-- 8️⃣ TV shows with more than 5 seasons
SELECT title, duration
FROM netflix
WHERE type = 'TV Show'
  AND CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) > 5;

-- 9️⃣ Number of content items in each genre
SELECT UNNEST(STRING_TO_ARRAY(listed_in,',')) AS genre, COUNT(*) AS total
FROM netflix
GROUP BY genre
ORDER BY total DESC;

-- 🔟 Top 5 years with highest avg content release in India
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

-- 1️⃣1️⃣ All movies that are documentaries
SELECT title
FROM netflix
WHERE type = 'Movie' AND listed_in ILIKE '%Documentaries%';

-- 1️⃣2️⃣ Content without a director
SELECT title
FROM netflix
WHERE director IS NULL OR TRIM(director) = '';

-- 1️⃣3️⃣ Number of movies with 'Salman Khan' in last 10 years
SELECT COUNT(*) AS total, title
FROM netflix
WHERE type = 'Movie'
  AND casts ILIKE '%Salman Khan%'
  AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10
GROUP BY title;

-- 1️⃣4️⃣ Top 10 actors in Indian movies who have appeared in the highest number of movies
WITH actor_list AS (
  SELECT UNNEST(STRING_TO_ARRAY(casts, ', ')) AS actor
  FROM netflix
  WHERE type = 'Movie' AND country LIKE '%India%' AND casts IS NOT NULL
)
SELECT actor, COUNT(*) AS movie_count
FROM actor_list
GROUP BY actor
ORDER BY movie_count DESC
LIMIT 10;

-- 1️⃣5️⃣ Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field
-- Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.
SELECT 
  CASE 
    WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
    ELSE 'Good'
  END AS category,
  COUNT(*) AS total
FROM netflix
GROUP BY category;
