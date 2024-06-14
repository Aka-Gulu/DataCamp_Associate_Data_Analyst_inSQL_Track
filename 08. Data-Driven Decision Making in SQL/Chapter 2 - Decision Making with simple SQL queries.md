### First account for each country.
___________________________________
```sql
SELECT country, -- For each country report the earliest date when an account was created
	min(date_account_start) AS first_account
FROM customers
GROUP BY country
ORDER BY first_account;
```

### Average movie ratings
___________________________________
```sql
SELECT movie_id, 
       avg(rating)    -- Calculate average rating per movie
FROM renting
group by movie_id;

SELECT movie_id, 
       AVG(rating) AS avg_rating, -- Use as alias avg_rating
       count(rating) as number_rating,                -- Add column for number of ratings with alias number_rating
       count(renting_id) as number_renting                -- Add column for number of movie rentals with alias number_renting
FROM renting
GROUP BY movie_id;

SELECT movie_id, 
       AVG(rating) AS avg_rating,
       COUNT(rating) AS number_ratings,
       COUNT(*) AS number_renting
FROM renting
GROUP BY movie_id
order by avg_rating desc; -- Order by average rating in decreasing order
```

### Average rating per customer
___________________________________
```sql
SELECT customer_id, -- Report the customer_id
      avg(rating),  -- Report the average rating per customer
      count(rating),  -- Report the number of ratings per customer
      count(renting_id)  -- Report the number of movie rentals per customer
FROM renting
GROUP BY customer_id
having count(renting_id) > 7 -- Select only customers with more than 7 movie rentals
ORDER BY avg(rating) desc; -- Order by the average rating in ascending order
```

### Join renting and customers
___________________________________
```sql
SELECT * -- Join renting with customers
FROM renting as r
LEFT JOIN customers as c
ON r.customer_id = c.customer_id ;

SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
where c.country = 'Belgium'; -- Select only records from customers coming from Belgium

SELECT avg(rating) -- Average ratings of customers from Belgium
FROM renting AS r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
WHERE c.country='Belgium';
```

### Aggregating revenue, rentals and active customers
___________________________________
```sql
SELECT *
FROM renting AS r
join movies AS m -- Choose the correct join statment
ON r.movie_id = m.movie_id ;

SELECT 
	sum(m.renting_price), -- Get the revenue from movie rentals
	count(*), -- Count the number of rentals
	count(distinct(customer_id))  -- Count the number of customers
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id;

SELECT 
	SUM(m.renting_price), 
	COUNT(*), 
	COUNT(DISTINCT r.customer_id)
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id
-- Only look at movie rentals in 2018
WHERE EXTRACT(YEAR FROM date_renting) = 2018 ;
```

### Movies and actors
___________________________________
```sql
SELECT m.title, -- Create a list of movie titles and actor names
       a.name
FROM actsin as ai
LEFT JOIN movies AS m
ON m.movie_id = ai.movie_id
LEFT JOIN actors AS a
ON a.actor_id = ai.actor_id ;
```

### Income from movies
___________________________________
```sql
SELECT m.title, -- Use a join to get the movie title and price for each movie rental
       m.renting_price
FROM renting AS r
LEFT JOIN movies AS m
ON r.movie_id = m.movie_id;

SELECT rm.title, -- Report the income from movie rentals for each movie 
       SUM(rm.renting_price) AS income_movie
FROM
       (SELECT m.title, 
               m.renting_price
       FROM renting AS r
       LEFT JOIN movies AS m
       ON r.movie_id=m.movie_id) AS rm
GROUP BY rm.title
ORDER BY income_movie DESC; -- Order the result by decreasing income
```

### Age of actors from the USA
___________________________________
```sql
SELECT a.gender, -- Report for male and female actors from the USA 
       min(a.year_of_birth), -- The year of birth of the oldest actor
       max(a.year_of_birth) -- The year of birth of the youngest actor
FROM
   (select * -- Use a subsequen SELECT to get all information about actors from the USA
   from actors
   where nationality = 'USA')
   as a -- Give the table the name a
GROUP BY a.gender;
```

### Identify favorite movies for a group of customers
___________________________________
```sql
SELECT *
FROM renting AS r
LEFT JOIN customers as c   -- Add customer information
on c.customer_id = r.customer_id
LEFT JOIN movies as m   -- Add movie information
on r.movie_id = m.movie_id;

SELECT *
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
where date_of_birth between '1970-01-01' and '1979-12-31'; -- Select customers born in the 70s

SELECT m.title, 
COUNT(*), -- Report number of views per movie
AVG(r.rating) -- Report the average rating per movie
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
group by m.title;


SELECT m.title, 
COUNT(*),
AVG(r.rating) 
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE c.date_of_birth BETWEEN '1970-01-01' AND '1979-12-31'
GROUP BY m.title
having count(*) > 1 -- Remove movies with only one rental
order by AVG(r.rating)  desc; -- Order with highest rating first
```

### Identify favorite actors for Spain
___________________________________
```sql
SELECT *
FROM renting as r 
LEFT JOIN customers as c  -- Augment table renting with information about customers 
on r.customer_id = c.customer_id
LEFT JOIN actsin as ai  -- Augment the table renting with the table actsin
on r.movie_id = ai.movie_id
LEFT JOIN actors as a  -- Augment table renting with information about actors
on ai.actor_id = a.actor_id;

SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id

GROUP BY a.name, c.gender -- For each actor, separately for male and female customers
HAVING AVG(r.rating) IS NOT NULL 
and COUNT(*) >5 -- Report only actors with more than 5 movie rentals
ORDER BY avg_rating DESC, number_views DESC;

SELECT a.name,  c.gender,
       COUNT(*) AS number_views, 
       AVG(r.rating) AS avg_rating
FROM renting as r
LEFT JOIN customers AS c
ON r.customer_id = c.customer_id
LEFT JOIN actsin as ai
ON r.movie_id = ai.movie_id
LEFT JOIN actors as a
ON ai.actor_id = a.actor_id
where c.country = 'Spain' -- Select only customers from Spain
GROUP BY a.name, c.gender
HAVING AVG(r.rating) IS NOT NULL 
  AND COUNT(*) > 5 
ORDER BY avg_rating DESC, number_views DESC;
```

### KPIs per country
___________________________________
```sql
SELECT *
FROM renting as r -- Augment the table renting with information about customers
LEFT JOIN customers as c
on c.customer_id = r.customer_id
LEFT JOIN movies as m -- Augment the table renting with information about movies
on r.movie_id = m.movie_id
where r.date_renting >= '2019-01-01'; -- Select only records about rentals since the beginning of 2019

SELECT 
	c.country,                     -- For each country report
	count(r.movie_id) AS number_renting, -- The number of movie rentals
	avg(r.rating) AS average_rating, -- The average rating
	sum(m.renting_price) AS revenue         -- The revenue from movie rentals
FROM renting AS r
LEFT JOIN customers AS c
ON c.customer_id = r.customer_id
LEFT JOIN movies AS m
ON m.movie_id = r.movie_id
WHERE date_renting >= '2019-01-01'
group by c.country;
```
