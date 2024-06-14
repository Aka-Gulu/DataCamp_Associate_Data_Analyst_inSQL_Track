### Groups of customers
_________________________
```sql
SELECT gender, -- Extract information of a pivot table of gender and country for the number of customers
	   country,
	   count(*)
FROM customers
GROUP BY CUBE (gender, country)
ORDER BY country;
```

### Categories of movies
_________________________
```sql
SELECT genre,
       year_of_release,
       count(*)
FROM movies
group by cube(genre,year_of_release)
ORDER BY year_of_release;
```

### Analyzing average ratings
_________________________
```sql
-- Augment the records of movie rentals with information about movies and customers
SELECT *
FROM renting as r
LEFT JOIN movies as m
on r.movie_id = m.movie_id
LEFT JOIN customers as c
on r.customer_id = c.customer_id;

-- Calculate the average rating for each country
SELECT 
	avg(r.rating),
    c.country
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
group by c.country;

SELECT 
	c.country, 
	m.genre, 
	AVG(r.rating) AS avg_rating -- Calculate the average rating 
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
group by cube(c.country,m.genre); -- For all aggregation levels of country and genre
```

### Number of customers
_________________________
```sql
-- Count the total number of customers, the number of customers for each country, and the number of female and male customers for each country
SELECT country,
       gender,
	   COUNT(*)
FROM customers
group by rollup (country, gender)
order by  country, gender; -- Order the result by country and gender
```

### Analyzing preferences of genres across countries
_________________________
```sql
-- Join the tables
SELECT *
FROM renting AS r
left join movies AS m
ON r.movie_id = m.movie_id
left join customers AS c
ON r.customer_id = c.customer_id;

SELECT 
	c.country, -- Select country
	m.genre, -- Select genre
	avg(r.rating), -- Average ratings
	count(*)  -- Count number of movie rentals
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY (c.country,m.genre) -- Aggregate for each country and each genre
ORDER BY c.country, m.genre;

-- Group by each county and genre with OLAP extension
SELECT 
	c.country, 
	m.genre, 
	AVG(r.rating) AS avg_rating, 
	COUNT(*) AS num_rating
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
GROUP BY rollup (c.country,m.genre)
ORDER BY c.country, m.genre;
```

### Exploring nationality and gender of actors
_________________________
```sql
SELECT 
	nationality, -- Select nationality of the actors
    gender, -- Select gender of the actors
    count(*) -- Count the number of actors
FROM actors
GROUP BY GROUPING SETS ((nationality), (gender), ()); -- Use the correct GROUPING SETS operation
```

### Exploring rating by country and gender
_________________________
```sql
SELECT 
	c.country, -- Select country, gender and rating
    c.gender,
    r.rating
FROM renting AS r
left join customers AS c -- Use the correct join
using (customer_id);

SELECT 
	c.country, 
    c.gender,
	avg(r.rating) -- Calculate average rating
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
group by c.country,c.gender -- Order and group by country and gender
ORDER BY c.country,c.gender;

SELECT 
	c.country, 
    c.gender,
	AVG(r.rating)
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
group by grouping sets((c.country,c.gender)); -- Group by country and gender with GROUPING SETS

SELECT 
	c.country, 
    c.gender,
	AVG(r.rating)
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
-- Report all info from a Pivot table for country and gender
GROUP BY GROUPING SETS ((country, gender), (country),(gender),());
```

### Customer preference for genres
_________________________
```sql
SELECT *
FROM renting AS r
left join movies as m -- Augment the table with information about movies
using(movie_id);

SELECT *
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE r.movie_id IN ( -- Select records of movies with at least 4 ratings
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING count(rating) >=4)
AND r.date_renting >= '2018-04-01'; -- Select records of movie rentals since 2018-04-01

SELECT m.genre, -- For each genre, calculate:
	   avg(r.rating) as avg_rating, -- The average rating and use the alias avg_rating
	   count(r.rating) as n_rating, -- The number of ratings and use the alias n_rating
	   count(*) as n_rentals,     -- The number of movie rentals and use the alias n_rentals
	   count(distinct(m.title)) as n_movies -- The number of distinct movies and use the alias n_movies
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >= 3)
AND r.date_renting >= '2018-01-01'
group by m.genre;

SELECT genre,
	   AVG(rating) AS avg_rating,
	   COUNT(rating) AS n_rating,
       COUNT(*) AS n_rentals,     
	   COUNT(DISTINCT m.movie_id) AS n_movies 
FROM renting AS r
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >= 3 )
AND r.date_renting >= '2018-01-01'
GROUP BY genre
order by avg_rating desc; -- Order the table by decreasing average rating
```

### Customer preference for actors
_________________________
```sql
-- Join the tables
SELECT *
FROM renting AS r
LEFT JOIN actsin AS ai
using(movie_id)
LEFT JOIN actors AS a
using(actor_id);

SELECT a.nationality,
       a.gender,
	   avg(r.rating) AS avg_rating, -- The average rating
	   count(r.rating) AS n_rating, -- The number of ratings
	   count(*) AS n_rentals, -- The number of movie rentals
	   count(distinct a.name) AS n_actors -- The number of actors
FROM renting AS r
LEFT JOIN actsin AS ai
ON ai.movie_id = r.movie_id
LEFT JOIN actors AS a
ON ai.actor_id = a.actor_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >=4 )
AND r.date_renting >= '2018-04-01'
GROUP BY a.nationality,a.gender; -- Report results for each combination of the actors' nationality and gender

SELECT a.nationality,
       a.gender,
	   AVG(r.rating) AS avg_rating,
	   COUNT(r.rating) AS n_rating,
	   COUNT(*) AS n_rentals,
	   COUNT(DISTINCT a.actor_id) AS n_actors
FROM renting AS r
LEFT JOIN actsin AS ai
ON ai.movie_id = r.movie_id
LEFT JOIN actors AS a
ON ai.actor_id = a.actor_id
WHERE r.movie_id IN ( 
	SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(rating) >= 4)
AND r.date_renting >= '2018-04-01'
GROUP BY cube(a.nationality,a.gender); -- Provide results for all aggregation levels represented in a pivot table
```
