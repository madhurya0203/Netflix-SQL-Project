# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/madhurya0203/Netflix-SQL-Project/blob/main/Netflix%20Logo.jpg)

## ## Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives

- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

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
```

## Business Problems and Solutions

### 1.Count the number of movies vs TV shows
'''sql
Select 
	type, 
	count(*) as total_content 
	from netflix
group by type
'''

**Objective:** Determine the distribution of content types on Netflix

### 2.	Find the most common rating for movies and tv shows
'''sql
select type,
		rating
from

		(select 
		    type,
		    rating,
		    count(*) as count,
		    rank() over (
		        partition by type 
		        order by count(*) desc
		    ) as ranking
		from netflix
		group by type, rating) as t1
where
	ranking = 1
 '''

 **Objective:** Identify the most frequently occuring rating for each type of content

### 3.List all the movies released in a specific year
'''sql
select * from netflix
where type = 'Movie' and release_year = 2020

###4.	Find the top 5 countries with the most content on Netflix
'''sql
select 
    country,
    count(*) as total_content
from netflix
where country is not null
group by country
order by total_content desc
limit 5;
'''

**Objective:** Identify the top 5 countries with the highest number of content items.

### 6.Identify the longest movie or TV show duration
select 
    title,
    type,
    duration
from netflix
where duration is not null
order by 
    case 
        when duration like '%season%' then cast(split_part(duration, ' ', 1) as integer) * 1000
        when duration like '%min%' then cast(split_part(duration, ' ', 1) as integer)
        else 0
    end desc
limit 1;
'''

### 7. Find the content added in the last 5 years
'''sql
select *
from netflix
where date_added is not null
  and to_date(date_added, 'month dd, yyyy') >= current_date - interval '5 years';
  '''

### 8. Find all the movies/TV shows directed by ‘Rajiv Chilaka’
'''sql
select * from netflix
	where director = 'Rajiv Chilaka'
 '''

### 9. List all the TV shows with more than 5 seasons
'''sql
select title, duration
from netflix
where type = 'tv show'
  and duration ilike '%season%'
  and cast(split_part(duration, ' ', 1) as integer) > 5;
  '''

### 10.Count the number of content items in each genre
'''sql
select listed_in as genre, count(*) as content_count
from netflix
group by listed_in
order by content_count desc;
'''

### 11. Find all the average release year for content produced in a specific country
'''sql
select country, avg(release_year) as avg_release_year
from netflix
where country = 'india'
group by country;
12.	List all the movies that are documentaries
select *
from netflix
where type = 'movie'
  and listed_in ilike '%documentaries%';
  '''

###13.	Find all content without a director
'''sql
select *
from netflix
where director is null or director = '';
'''

###14.	Find how many movies actor ‘Salman Khan’ has appeared in the last 10 years
'''sql
select * from netflix
	where
		casts ilike '%Salman Khan%'
		and
		release_year > extract(year from current_date)-10
  '''

### 15. Find the top 10 actors who have appeared in the highest number of movies produced in India
'''sql
select
unnest (string_to_array(casts, ','))as actors,
count(*)as total_content
from netflix
where country ilike '%india%'
group by 1
order by 2 desc
limit 10
'''

### 16. Categorize the content based on the presence of the keywords ‘kill’ and ‘violence’ in the description field. Label content containing these keywords as ‘Bad’, and all other content as ‘Good’. Count the number of items in each category.
'''sql
select 
    case 
        when description ilike '%kill%' or description ilike '%violence%' then 'bad'
        else 'good'
    end as category,
    count(*) as content_count
from netflix
group by category;
'''
