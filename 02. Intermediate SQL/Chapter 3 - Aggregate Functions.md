### Practice with aggregate functions
______________________________________
```sql
-- Query the sum of film durations
select SUM(duration) as total_duration
from films

-- Calculate the average duration of all films
select avg(duration) as average_duration from films

-- Find the latest release_year
select max(release_year) as latest_year from films

-- Find the duration of the shortest film
select min(duration) as shortest_film from films
```

### Combining aggregate functions with WHERE
______________________________________
```sql
-- Calculate the sum of gross from the year 2000 or later
select sum(gross) as total_gross from films
where release_year >= 2000

-- Calculate the average gross of films that start with A
select avg(gross) as avg_gross_A from films
where title like'A%'

-- Calculate the lowest gross film in 1994
select min(gross) as lowest_gross from films
where release_year = 1994

-- Calculate the highest gross film released between 2000-2012
select max(gross) as highest_gross from films
where release_year between 2000 and 2012
```

### Using ROUND()
______________________________________
```sql
-- Round the average number of facebook_likes to one decimal places
select round(avg(facebook_likes),1) as avg_facebook_likes from reviews
```

### ROUND() with a negative parameter
______________________________________
```sql
-- Calculate the average budget rounded to the thousands
select round(avg(budget),-3) as avg_budget_thousands from films
```

### Aliasing with functions
______________________________________
```sql
-- Calculate the title and duration_hours from films
SELECT title, duration/60.0 as duration_hours
FROM films;

-- Calculate the percentage of people who are no longer alive
SELECT count(deathdate) * 100.0 / count(*) AS percentage_dead
FROM people;

-- Find the number of decades in the films table
SELECT round((max(release_year) - min(release_year))/10) as number_of_decades
FROM films;
```

### Rounding results
______________________________________
```sql
-- Round duration_hours to two decimal places
SELECT title, round(duration / 60.0,2) AS duration_hours
FROM films;
```
