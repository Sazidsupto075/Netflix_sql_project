# Netflix Movies and Tv Shows Data Analysis with SQL

![NETFLIX LOGO](https://github.com/Sazidsupto075/Netflix_sql_project/blob/main/Netflix_logo.svg.png)


##Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

##Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.
Dataset
The data for this project is sourced from the Kaggle dataset:


##Schema
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

SELECT  * from netflix;

SELECT COUNT(*) AS total_content
FROM netflix;

SELECT DISTINCT type
FROM netflix;

-- 15 Business Problems & Solutions

1. Count the number of Movies vs TV Shows

 SELECT type,COUNT(*) AS total_content
 from netflix
 GROUP BY type;

 Objective: Determine the distribution of content types on Netflix.


2. Find the most common rating for movies and TV shows

SELECT type, rating
from 
(  
	SELECT type, rating, count(*),
	RANK() OVER(PARTITION by type ORDER BY count(*) DESC ) as ranking
	FROM netflix
	GROUP BY type, rating
) as t1

WHERE ranking = 1;

Objective: Identify the most frequently occurring rating for each type of content.




3. List all movies released in a specific year (e.g., 2020)

-- filter 2020
--type movies

SELECT * 
FROM netflix
where type= 'Movie'
AND release_year = '2020'

Objective: Retrieve all movies released in a specific year.

4. Find the top 5 countries with the most content on Netflix

SELECT distinct(UNNEST(STRING_TO_ARRAY(country,','))) as new_country, 
COUNT(show_id) as total_content
FROM netflix
GROUP BY new_country
ORDER BY 2 DESC
LIMIT 5;

Objective: Identify the top 5 countries with the highest number of content items.


5. Identify the longest movie

select * from 
 (select distinct title as movie,
	split_part(duration,' ',1):: numeric as duration
	from netflix
	WHERE type='Movie'
  ) as subquery
 WHERE duration = (SELECT MAX(SPLIT_PART(duration,' ',1):: numeric) from netflix)

Objective: Find the movie with the longest duration.

6. Find content added in the last 5 years

SELECT *
FROM netflix
WHERE  TO_DATE(date_added, 'MONTH DD, YYYY')  >=  CURRENT_DATE - INTERVAL '5 years'


Objective: Retrieve content added to Netflix in the last 5 years.

7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

SELECT * 
FROM netflix
WHERE director LIKE '%Rajiv Chilaka%';

Objective: List all content directed by 'Rajiv Chilaka'.


8. List all TV shows with more than 5 seasons

SELECT * 
FROM netflix
WHERE type = 'TV Show'
AND SPLIT_PART(duration, ' ',1):: numeric > 5

Objective: Identify TV shows with more than 5 seasons.


9. Count the number of content items in each genre

SELECT 
 UNNEST(STRING_TO_ARRAY(listed_in, ',')) as genre,
 COUNT(*) as count_of_contents
 FROM netflix
 GROUP BY genre
 Order BY count_of_contents DESC;

 Objective: Count the number of content items in each genre.

10.Find each year and the average numbers of content release in India on netflix. 
return top 5 year with highest avg content release!

 SELECT * FROM netflix

 SELECT EXTRACT(YEAR FROM TO_DATE(date_added, 'MONTH DD,YYYY')) as year, 
 COUNT(*) as content_per_year,
  ROUND((COUNT(*):: numeric / (SELECT COUNT(*) from netflix WHERE country LIKE '%India%'):: numeric) * 100,2) as avg_content_per_year
 FROM netflix
 WHERE country LIKE '%India%'
 GROUP BY year
 ORDER BY 2 DESC

 Objective: Calculate and rank years by the average number of content releases by India.



11. List all movies that are documentaries

 --SELECT * FROM netflix

SELECT * from netflix
WHERE listed_in ILIKE '%documentaries%'
AND type = 'Movie';

Objective: Retrieve all movies classified as documentaries.
 
 


12. Find all content without a director

SELECT * from netflix
WHERE director is null;

Objective: List content that does not have a director.

13. Find how many movies actor 'Salman Khan' appeared in last 10 years!

SELECT COUNT(*) as movies_appeared
FROM netflix
WHERE casts ILIKE '%Salman Khan%'
AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10

Objective: Count the number of movies featuring 'Salman Khan' in the last 10 years.


14. Find the top 10 actors who have appeared in the highest number of movies produced in India.



SELECT UNNEST(STRING_TO_ARRAY(casts, ',')) as actors,
COUNT(*) as Total_appearance
FROM netflix
WHERE country ILIKE '%INDIA%'
AND type = 'Movie'
GROUP BY actors 
ORDER BY total_appearance DESC
LIMIT 10;

Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.



15.Categorize the content based on the presence of the keywords 'kill' and 'violence' 
in the description field.Label content containing these keywords as 'Bad' and all other content as 'Good'. 
Count how many items fall into each category.


SELECT title, type
FROM netflix 
WHERE description ILIKE '%kill%' OR description ILIKE '%violence%'

Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.



##Findings and Conclusion
Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.
This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.
