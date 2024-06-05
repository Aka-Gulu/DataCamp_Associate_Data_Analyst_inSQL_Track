### Your first join
______________________________
```sql
-- Select all columns from cities
select * from cities

SELECT * 
FROM cities
-- Inner join to countries
inner join countries
-- Match on country codes
on cities.country_code = countries.code

-- Select name fields (with alias) and region 
SELECT cities.name as city,countries.name as country, countries.region
FROM cities
INNER JOIN countries
on cities.country_code = countries.code;
```

### Joining with aliased tables
______________________________
```sql
-- Select fields with aliases
select c.code as country_code,c.name,e.year,e.inflation_rate
FROM countries AS c
-- Join to economies (alias e)
inner join economies as e
-- Match on code field using table aliases
on c.code = e.code
```

### USING in action
______________________________
```sql
SELECT c.name AS country, l.name AS language, official
FROM countries AS c
INNER JOIN languages AS l
-- Match using the code column
using (code)
```

### Inspecting a relationship
______________________________
```sql
-- Select country and language names, aliased
select c.name as country,l.name as language
-- From countries (aliased)
from countries as c
-- Join to languages (aliased)
inner join languages as l
-- Use code as the joining field with the USING keyword
using(code);

-- Rearrange SELECT statement, keeping aliases
SELECT l.name AS language, c.name AS country
FROM countries AS c
INNER JOIN languages AS l
USING(code)
-- Order the results by language
order by language
```

### Joining multiple tables
______________________________
```sql
-- Select relevant fields
select name,year,fertility_rate from countries as c
-- Inner join countries and populations, aliased, on code
inner join populations as p
on c.code = p.country_code

-- Select fields
SELECT name, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
-- Join to economies (as e)
inner join economies as e
-- Match on country code
using(code);
```

### Checking multi-table joins
______________________________
```sql
SELECT name, e.year, fertility_rate, unemployment_rate
FROM countries AS c
INNER JOIN populations AS p
ON c.code = p.country_code
INNER JOIN economies AS e
ON c.code = e.code
-- Add an additional joining condition such that you are also joining on year
	and e.year = p.year;
```

