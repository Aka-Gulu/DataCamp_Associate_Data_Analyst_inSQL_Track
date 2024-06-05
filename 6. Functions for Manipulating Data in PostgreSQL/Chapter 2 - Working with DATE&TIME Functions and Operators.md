### Adding and subtracting date and time values
________________________________________________
```sql
SELECT f.title, f.rental_duration,
    -- Calculate the number of days rented
    r.return_date - r.rental_date AS days_rented
FROM film AS f
     INNER JOIN inventory AS i ON f.film_id = i.film_id
     INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
ORDER BY f.title;

SELECT f.title, f.rental_duration,
    -- Calculate the number of days rented
	age(r.return_date, r.rental_date) AS days_rented
FROM film AS f
	INNER JOIN inventory AS i ON f.film_id = i.film_id
	INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
ORDER BY f.title;
```

### INTERVAL arithmetic
________________________________________________
```sql
SELECT
	f.title,
 	-- Convert the rental_duration to an interval
    INTERVAL '1' day * f.rental_duration,
 	-- Calculate the days rented as we did previously
    r.return_date - r.rental_date AS days_rented
FROM film AS f
    INNER JOIN inventory AS i ON f.film_id = i.film_id
    INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
-- Filter the query to exclude outstanding rentals
WHERE r.return_date is not NULL
ORDER BY f.title;
```

### Calculating the expected return date
________________________________________________
```sql
SELECT
    f.title,
	r.rental_date,
    f.rental_duration,
    -- Add the rental duration to the rental date
    INTERVAL '1' day * f.rental_duration + r.rental_date AS expected_return_date,
    r.return_date
FROM film AS f
    INNER JOIN inventory AS i ON f.film_id = i.film_id
    INNER JOIN rental AS r ON i.inventory_id = r.inventory_id
ORDER BY f.title;
```

### Working with the current date and time
________________________________________________
```sql
-- Select the current timestamp
SELECT now();

-- Select the current date
SELECT current_date;

--Select the current timestamp without a timezone
SELECT cast( NOW() AS timestamp )

SELECT 
	-- Select the current date
	current_date,
    -- CAST the result of the NOW() function to a date
    cast( now() AS date )
```

### Manipulating the current date and time
________________________________________________
```sql
--Select the current timestamp without timezone
SELECT current_timestamp::timestamp AS right_now;

SELECT
	CURRENT_TIMESTAMP::timestamp AS right_now,
    INTERVAL '5 days' + CURRENT_TIMESTAMP AS five_days_from_now;

SELECT
	CURRENT_TIMESTAMP(2)::timestamp AS right_now,
    interval '5 days' + CURRENT_TIMESTAMP(2) AS five_days_from_now;
```

### Using EXTRACT
________________________________________________
```sql
SELECT 
  -- Extract day of week from rental_date
  extract(dow from rental_date) AS dayofweek 
FROM rental 
LIMIT 100;

-- Extract day of week from rental_date
SELECT 
  EXTRACT(dow FROM rental_date) AS dayofweek, 
  -- Count the number of rentals
  count(*) as rentals 
FROM rental 
GROUP BY 1;
```

### Using DATE_TRUNC
________________________________________________
```sql
-- Truncate rental_date by year
SELECT date_trunc('year', rental_date) AS rental_year
FROM rental;

-- Truncate rental_date by month
SELECT date_trunc('month', rental_date) AS rental_month
FROM rental;

-- Truncate rental_date by day of the month 
SELECT date_trunc('day', rental_date) AS rental_day 
FROM rental;


SELECT 
  DATE_TRUNC('day', rental_date) AS rental_day,
  -- Count total number of rentals 
  count(*) AS rentals 
FROM rental
GROUP BY 1;
```

### Putting it all together
________________________________________________
```sql
SELECT 
  -- Extract the day of week date part from the rental_date
  extract(dow from rental_date) AS dayofweek,
  AGE(return_date, rental_date) AS rental_days
FROM rental AS r 
WHERE 
  -- Use an INTERVAL for the upper bound of the rental_date 
  rental_date BETWEEN CAST('2005-05-01' AS timestamp)
   AND CAST('2005-05-01' AS timestamp) + INTERVAL '90 day';

SELECT 
  c.first_name || ' ' || c.last_name AS customer_name,
  f.title,
  r.rental_date,
  -- Extract the day of week date part from the rental_date
  EXTRACT(dow FROM r.rental_date) AS dayofweek,
  AGE(r.return_date, r.rental_date) AS rental_days,
  -- Use DATE_TRUNC to get days from the AGE function
  CASE WHEN date_trunc('day', AGE(r.return_date, r.rental_date)) > 
  -- Calculate number of d
    f.rental_duration * INTERVAL '1' day 
  THEN TRUE 
  ELSE FALSE END AS past_due 
FROM 
  film AS f 
  INNER JOIN inventory AS i 
  	ON f.film_id = i.film_id 
  INNER JOIN rental AS r 
  	ON i.inventory_id = r.inventory_id 
  INNER JOIN customer AS c 
  	ON c.customer_id = r.customer_id 
WHERE 
  -- Use an INTERVAL for the upper bound of the rental_date 
  r.rental_date BETWEEN CAST('2005-05-01' AS DATE) 
  AND CAST('2005-05-01' AS DATE) + INTERVAL '90 day';
```
