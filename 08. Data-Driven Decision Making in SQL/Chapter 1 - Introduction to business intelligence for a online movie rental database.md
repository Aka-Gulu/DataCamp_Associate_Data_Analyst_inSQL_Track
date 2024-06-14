### Exploring the table renting
_________________________________
```sql
SELECT *  -- Select all
from renting;        -- From table renting

SELECT rating,  -- Select all columns needed to compute the average rating per movie
       movie_id
FROM renting;
```

### Working with dates
_________________________________
```sql
SELECT *
FROM renting
where date_renting = '2018-10-09'; -- Movies rented on October 9th, 2018

SELECT *
FROM renting
where date_renting >= '2018-04-01' and date_renting < '2018-09-01'; -- from beginning April 2018 to end August 2018

SELECT *
FROM renting
WHERE date_renting BETWEEN '2018-04-01' AND '2018-08-31'
order by date_renting desc; -- Order by recency in decreasing order
```

### Selecting movies
_________________________________
```sql
SELECT *
FROM movies
where genre != 'Drama'; -- All genres except drama

SELECT *
FROM movies
where title in ('Showtime','Love Actually','The Fighter'); -- Select all movies with the given titles

SELECT *
FROM movies
order by renting_price ; -- Order the movies by increasing renting price
```

### Select from renting
_________________________________
```sql
SELECT *
FROM renting
WHERE date_renting between '2018-01-01' and '2018-12-31' -- Renting in 2018
AND rating is not null; -- Rating exists
```

### Summarizing customer information
_________________________________
```sql
SELECT count(*) -- Count the total number of customers
FROM customers
WHERE date_of_birth between '1980-01-01' and '1989-12-31'; -- Select customers born between 1980-01-01 and 1989-12-31

SELECT count(*)   -- Count the total number of customers
FROM customers
where country = 'Germany'; -- Select all customers from Germany

SELECT count(distinct(country))   -- Count the number of countries
FROM customers;
```

### Ratings of movie 25
_________________________________
```sql
SELECT min(rating) min_rating, -- Calculate the minimum rating and use alias min_rating
	   max(rating) max_rating, -- Calculate the maximum rating and use alias max_rating
	   avg(rating) avg_rating, -- Calculate the average rating and use alias avg_rating
	   count(rating) number_ratings -- Count the number of ratings and use alias number_ratings
FROM renting
where movie_id = 25; -- Select all records of the movie with ID 25
```

### Examining annual rentals
_________________________________
```sql
select * -- Select all records of movie rentals since January 1st 2019
from renting
where date_renting >= '2019-01-01';

SELECT 
	count(*), -- Count the total number of rented movies
	avg(rating) -- Add the average rating
FROM renting
WHERE date_renting >= '2019-01-01';

SELECT 
	COUNT(*) as number_renting, -- Give it the column name number_renting
	AVG(rating) as average_rating -- Give it the column name average_rating
FROM renting
WHERE date_renting >= '2019-01-01';

SELECT 
	COUNT(*) AS number_renting,
	AVG(rating) AS average_rating, 
    count(rating) AS number_ratings -- Add the total number of ratings here.
FROM renting
WHERE date_renting >= '2019-01-01';
```
