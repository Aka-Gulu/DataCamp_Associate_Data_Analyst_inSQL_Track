### Often rented movies
_______________________________
```sql
SELECT movie_id -- Select movie IDs with more than 5 views
FROM renting
GROUP BY movie_id
HAVING COUNT(*) > 5;

SELECT *
FROM movies
where movie_id in  -- Select movie IDs from the inner query
	(SELECT movie_id
	FROM renting
	GROUP BY movie_id
	HAVING COUNT(*) > 5)
```

### Frequent customers
_______________________________
```sql
SELECT *
FROM customers
where customer_id in           -- Select all customers with more than 10 movie rentals
	(SELECT customer_id
	FROM renting
	GROUP BY customer_id
	having count(*) >10);
```

### Movies with rating above average
_______________________________
```sql
select avg(rating) -- Calculate the total average rating
from renting

SELECT movie_id, -- Select movie IDs and calculate the average rating 
       avg(rating)
FROM renting
group by movie_id
having avg(rating)>           -- Of movies with rating above average
	(SELECT AVG(rating)
	FROM renting);

SELECT title -- Report the movie titles of all movies with average rating higher than the total average
FROM movies
WHERE movie_id in
	(SELECT movie_id
	 FROM renting
     GROUP BY movie_id
     HAVING AVG(rating) > 
		(SELECT AVG(rating)
		 FROM renting));
```

### Analyzing customer behavior
_______________________________
```sql
-- Count movie rentals of customer 45
select count(*)
from renting as r
where customer_id = 45;

-- Select customers with less than 5 movie rentals
SELECT *
FROM customers as c
WHERE 5 > 
	(SELECT count(*)
	FROM renting as r
	WHERE r.customer_id = c.customer_id);
```

### Customers who gave low ratings
_______________________________
```sql
-- Calculate the minimum rating of customer with ID 7
SELECT min(rating)
from renting
where customer_id = 7;

SELECT *
FROM customers as c
WHERE 4 > -- Select all customers with a minimum rating smaller than 4 
	(SELECT MIN(rating)
	FROM renting AS r
	WHERE r.customer_id = c.customer_id);
```

### Movies and ratings with correlated queries
_______________________________
```sql
SELECT *
FROM movies as m
WHERE 5 < -- Select all movies with more than 5 ratings
	(SELECT count(rating)
	FROM renting as r
	WHERE r.movie_id = m.movie_id );

SELECT *
FROM movies AS m
WHERE 8 < -- Select all movies with an average rating higher than 8
	(SELECT avg(rating)
	FROM renting AS r
	WHERE r.movie_id = m.movie_id);
```

### Customers with at least one rating
_______________________________
```sql
-- Select all records of movie rentals from customer with ID 115
select *
from renting
where customer_id = 115 ;

SELECT *
FROM renting
WHERE rating is not null -- Exclude those with null ratings
AND customer_id = 115;

SELECT *
FROM renting
WHERE rating is not null -- Exclude null ratings
and customer_id = 1; -- Select all ratings from customer with ID 1

SELECT *
FROM customers as c -- Select all customers with at least one rating
WHERE exists
	(SELECT *
	FROM renting AS r
	WHERE rating IS NOT NULL 
	AND r.customer_id = c.customer_id);
```

### Actors in comedies
_______________________________
```sql
SELECT *  -- Select the records from the table `actsin` of all actors who play in a Comedy
FROM actsin AS ai
left join movies as m
on ai.movie_id = m.movie_id
WHERE m.genre = 'Comedy';

SELECT *
FROM actsin AS ai
LEFT JOIN movies AS m
ON m.movie_id = ai.movie_id
WHERE m.genre = 'Comedy'
and ai.actor_id = 1; -- Select only the actor with ID 1

SELECT *
FROM actors as a
WHERE exists
	(SELECT *
	 FROM actsin AS ai
	 LEFT JOIN movies AS m
	 ON m.movie_id = ai.movie_id
	 WHERE m.genre = 'Comedy'
	 AND ai.actor_id = a.actor_id);

SELECT a.nationality,count(*) -- Report the nationality and the number of actors for each nationality
FROM actors AS a
WHERE EXISTS
	(SELECT ai.actor_id
	 FROM actsin AS ai
	 LEFT JOIN movies AS m
	 ON m.movie_id = ai.movie_id
	 WHERE m.genre = 'Comedy'
	 AND ai.actor_id = a.actor_id)
group by a.nationality;
```

### Young actors not coming from the USA
_______________________________
```sql
SELECT name,  -- Report the name, nationality and the year of birth
       nationality, 
       year_of_birth
FROM actors
where nationality != 'USA'; -- Of all actors who are not from the USA

SELECT name, 
       nationality, 
       year_of_birth
FROM actors
where year_of_birth > 1990; -- Born after 1990

SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE nationality <> 'USA'
union -- Select all actors who are not from the USA and all actors who are born after 1990
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > 1990;

SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE nationality <> 'USA'
intersect -- Select all actors who are not from the USA and who are also born after 1990
SELECT name, 
       nationality, 
       year_of_birth
FROM actors
WHERE year_of_birth > 1990;
```

### Dramas with high ratings
_______________________________
```sql
SELECT movie_id -- Select the IDs of all dramas
FROM movies
where genre = 'Drama';

SELECT movie_id,avg(rating) -- Select the IDs of all movies with average rating higher than 9
FROM renting
GROUP BY movie_id
having avg(rating) > 9;

SELECT movie_id
FROM movies
WHERE genre = 'Drama'
intersect  -- Select the IDs of all dramas with average rating higher than 9
SELECT movie_id
FROM renting
GROUP BY movie_id
HAVING AVG(rating)>9;

SELECT *
FROM movies
where movie_id in  -- Select all movies of genre drama with average rating higher than 9
   (SELECT movie_id
    FROM movies
    WHERE genre = 'Drama'
    INTERSECT
    SELECT movie_id
    FROM renting
    GROUP BY movie_id
    HAVING AVG(rating)>9);
```
