# Netflix Movies And Tv shows Data Analysis using SQL

![Netflix Logo](https://github.com/Deba024/Netflix_SQL_Project/blob/main/logo.png)

##  Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives
* Analyze the distribution of content types (movies vs TV shows).
* Identify the most common ratings for movies and TV shows.
* List and analyze content based on release years, countries, and durations.
* Explore and categorize content based on specific criteria and keywords.

## Dataset
The data for this project is sourced from the Kaggle dataset:
* Dataset Link: [(Netflix Dateset)](https://github.com/Deba024/Netflix_SQL_Project/blob/main/netflix_titles.csv)

## Schema
```sql

DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix(
	show_id	VARCHAR(10),
	type VARCHAR(15),
	title VARCHAR(150),
	director VARCHAR(210),
	casts VARCHAR(1000),
	country	VARCHAR(150),
	date_added DATE,
	release_year INT,
	rating VARCHAR(10),
	duration VARCHAR(15),
	listed_in VARCHAR(100),
	description VARCHAR(250)
);
```
## Business Problems and Solutions

### 1. Count the number of Movies vs TV Shows

```sql
SELECT
	type,
	COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```
**Objective:** Determine the distribution of content types on Netflix.

### 2. Find the most common rating for movies and TV shows

```sql
SELECT
	type,
	rating
FROM
(
	SELECT
		type,
		rating,
		COUNT(*),
		RANK() OVER(PARTITION BY type ORDER BY COUNT(*)DESC) AS ranking
	FROM netflix
	GROUP BY 1,2
) AS t1
WHERE ranking = 1;
```
**Objective:** Identify the most frequently occurring rating for each type of content.

### 3. List all movies released in a specific year (e.g., 2020)

```sql
SELECT 
	* 
FROM 
	netflix
where 
	type = 'Movie'
	AND
	release_year = 2020;
```
**Objective:** Retrieve all movies released in a specific year.

### 4. Find the top 5 countries with the most content on Netflix

```sql
SELECT 
	UNNEST(STRING_TO_ARRAY(country, ',')) AS New_country,
	COUNT(show_id) AS total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5;
```
**Objective:** Identify the top 5 countries with the highest number of content items.

### 5. Identify the longest movie

```sql
SELECT * FROM netflix
WHERE
	type = 'Movie'
	AND
	duration = (SELECT MAX(duration) FROM netflix);
```
**Objective:** Find the movie with the longest duration.

### 6. Find Content Added in the Last 5 Years

```sql
SELECT
	*
FROM netflix
WHERE
	date_added >= CURRENT_DATE - INTERVAL '5 years';
```
** Objective:** Retrieve content added to Netflix in the last 5 years.

### 7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

```sql
SELECT
	type,
	title
FROM netflix
WHERE director LIKE '%Rajiv Chilaka%';
```
** Objective:** List all content directed by 'Rajiv Chilaka'.

### 8. List all TV shows with more than 5 seasons

```sql
SELECT 
	*
	-- SPLIT_PART(duration, ' ', 1) AS Seasons
FROM netflix
WHERE
	type = 'TV Show'
	AND
	SPLIT_PART(duration, ' ', 1)::numeric > 5;
```
** Objective:** Identify TV shows with more than 5 seasons.

### 9. Count the number of content items in each genre

```sql
SELECT
	UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
	COUNT(show_id) AS total_content
FROM netflix
GROUP BY 1;
```
** Objective:** Count the number of content items in each genre.

### 10.Find each year and the average numbers of content release in India on netflix. 
return top 5 year with highest avg content release!

```sql
-- total content 33/972

SELECT 
	EXTRACT(YEAR FROM date_added) AS date,
	COUNT(*),
	ROUND(COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100, 0) AS Avg_content_per_year
FROM netflix
WHERE country = 'India'
GROUP BY 1;
```
** Objective:** Calculate and rank years by the average number of content releases by India.

### 11. List all movies that are documentaries

```sql
SELECT * FROM netflix
WHERE
	listed_in ILIKE '%documentaries%';
```
** Objective:** Retrieve all movies classified as documentaries.

### 12. Find all content without a director

```sql
SELECT * FROM netflix
WHERE
	director IS NULL;
```
** Objective:** List content that does not have a director.

### 13. Find how many movies actor 'Salman Khan' appeared in last 10 years!

```sql
SELECT * FROM netflix
WHERE
	casts ILIKE '%Salman Khan%'
	AND 
	release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```
** Objective:** Count the number of movies featuring 'Salman Khan' in the last 10 years.

### 14. Find the top 10 actors who have appeared in the highest number of movies produced in India.

```sql
SELECT
UNNEST(STRING_TO_ARRAY(casts, ',')) AS Actors,
COUNT(*) AS total_content
FROM netflix
WHERE country ILIKE 'India'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```
** Objective:** Identify the top 10 actors with the most appearances in Indian-produced movies.

### 15.
Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
the description field. Label content containing these keywords as 'Bad' and all other 
content as 'Good'. Count how many items fall into each category.

```sql
WITH new_table AS (
SELECT
	*,
	CASE
		WHEN description ILIKE '%kill%' OR
			 description ILIKE '%violence%' THEN 'Bad_Content'
		ELSE 'Good_Content'
	END Category
FROM netflix
)
SELECT
	Category,
	COUNT(*) AS total_content
FROM new_table
GROUP BY 1;
```
** Objective:** Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.





