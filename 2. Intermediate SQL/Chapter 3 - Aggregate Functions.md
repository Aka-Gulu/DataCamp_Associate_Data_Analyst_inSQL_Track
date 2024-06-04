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

