# Netflix Movies and TV shows Analysis

-- Netflix Project
[Netflix logo](https://github.com/faisalmandol/Netflix_SQL_Projects/blob/main/Netflix_Logo.png)

drop table if exists netflix;
create table netflix (
       show_id varchar(6),
	   type	   varchar(10),
	   title   varchar(150),
	   director varchar(210),
	   casts    varchar(1000),
	   country  varchar(150),
	   date_added varchar(50),
	   release_year int,
	   rating  varchar(10),
	   duration varchar(15),
	   listed_in varchar(100),
	   description varchar(250)
);

select * from netflix;

select count(*) as total_rows
from netflix;

-- 14 Business Problems & Solutions

-- 1. Count the number of Movies vs TV Shows

select type, count(*)
from netflix
group by type;

-- 2. Find the most common rating for movies and TV shows

with cte as (
     select type,
	        rating,
			count(*) as rating_count
	 from netflix
	 group by type, rating
),
cte2 as (

   select type,
          rating,
		  rating_count,
		  rank() over(partition by type order by rating_count desc) as rnk
   from cte		     
);

select type, 
       rating as most_frequent_rating
from cte2
where rnk = 1;

-- 3. List all movies released in a specific year (e.g., 2020)

select * 
from netflix
where type = 'Movie' and release_year = 2020;

-- 4. Find the top 5 countries with the most content on Netflix

select country, 
       count(*) as most_content
from netflix
where country is not null
group by country
order by most_content desc
limit 5;

-- 5. Identify the longest movie

select *
from netflix
where type = 'Movie' and duration is not null
order by split_part(duration, ' ', 1)::int desc
limit 1;

--6. Find content added in the last 5 years- 
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY')
      >= CURRENT_DATE - INTERVAL '5 years';

-- 7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

select * 
from netflix
where director = 'Rajiv Chilaka';

-- 8. List all TV shows with more than 5 seasons

select *
from netflix 
where type = 'TV Show' 
     and split_part(duration, ' ',1)::int > 5;
	 
-- 9. Count the number of content items in each genre

select listed_in as genre,
       count(*) as content
from netflix
group by listed_in;

-- 10. List all movies that are documentaries

select *
from netflix
where listed_in like '%Documentaries';

-- 11. Find all content without a director

select * 
from netflix
where director is null;

-- 12. Find how many movies actor 'Salman Khan' appeared in last 10 years!

select * 
from netflix
where casts like '%Salman Khan' 
     and 
	 release_year > extract(year from current_date) - 10;

-- 13. Find the top 10 actors who have appeared in the highest number of movies produced in India.

select unnest(string_to_array(casts, ',')) as actor,
       count(*)
from netflix
where country = 'India'
group by 1
order by 2 desc
limit 10;
 
-- 14. Categorize the content based on the presence of the keywords 'kill' and 'violence' in 
--     the description field. Label content containing these keywords as 'Bad' and all other 
--     content as 'Good'. Count how many items fall into each category.

SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description LIKE '%kill%' OR description LIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;

--- End of the project ---------------------------------------------------------------------------------------

