### This is a LEFT JOIN, right?
__________________________________
```sql
SELECT 
    c1.name AS city,
    code,
    c2.name AS country,
    region,
    city_proper_pop
FROM cities AS c1
-- Perform an inner join with cities as c1 and countries as c2 on country code
inner join countries as c2 
on c1.country_code = c2.code
ORDER BY code DESC;

SELECT 
	c1.name AS city, 
    code, 
    c2.name AS country,
    region, 
    city_proper_pop
FROM cities AS c1
-- Join right table (with alias)
left join countries as c2
ON c1.country_code = c2.code
ORDER BY code DESC;
```

### Building on your LEFT JOIN
__________________________________
```sql
SELECT name, region, gdp_percapita
FROM countries AS c
LEFT JOIN economies AS e
-- Match on code fields
on c.code = e.code
-- Filter for the year 2010
where year = 2010;

-- Select region, and average gdp_percapita as avg_gdp
select region, avg(gdp_percapita) as avg_gdp
FROM countries AS c
LEFT JOIN economies AS e
USING(code)
WHERE year = 2010
-- Group by region
GROUP BY region;

SELECT region, AVG(gdp_percapita) AS avg_gdp
FROM countries AS c
LEFT JOIN economies AS e
USING(code)
WHERE year = 2010
GROUP BY region
-- Order by descending avg_gdp
order by avg_gdp desc
-- Return only first 10 records
limit 10;
```

### Is this RIGHT?
__________________________________
```sql
-- Modify this query to use RIGHT JOIN instead of LEFT JOIN
SELECT countries.name AS country, languages.name AS language, percent
FROM languages
RIGHT JOIN countries
USING(code)
ORDER BY language;
```

### Comparing joins
__________________________________
```sql
SELECT name AS country, code, region, basic_unit
FROM countries
-- Join to currencies
full join currencies
USING (code)
-- Where region is North America or name is null
WHERE region = 'North America' or name is null
ORDER BY region;

SELECT name AS country, code, region, basic_unit
FROM countries
-- Join to currencies
left join currencies
USING (code)
WHERE region = 'North America' 
	OR name IS NULL
ORDER BY region;

SELECT name AS country, code, region, basic_unit
FROM countries
-- Join to currencies
inner join currencies
USING (code)
WHERE region = 'North America' 
	OR name IS NULL
ORDER BY region;
```

### Chaining FULL JOINs
__________________________________
```sql
SELECT 
	c1.name AS country, 
    region, 
    l.name AS language,
	basic_unit, 
    frac_unit
FROM countries as c1 
-- Full join with languages (alias as l)
full join languages as l
using (code)
-- Full join with currencies (alias as c2)
full join currencies as c2
using (code)
WHERE region LIKE 'M%esia';
```

### Histories and languages
__________________________________
```sql
SELECT c.name AS country, l.name AS language
-- Inner join countries as c with languages as l on code
from countries as c 
inner join languages as l
using(code)
WHERE c.code IN ('PAK','IND')
	AND l.code in ('PAK','IND');

SELECT c.name AS country, l.name AS language
FROM countries AS c        
-- Perform a cross join to languages (alias as l)
cross join languages as l
WHERE c.code in ('PAK','IND')
	AND l.code in ('PAK','IND');
```

### Choosing your join
__________________________________
```sql
-- Select fields
SELECT c.name AS country, region, life_expectancy AS life_exp
-- From countries (alias as c)
FROM countries as c
  -- Join to populations (alias as p)
  LEFT JOIN populations as p
    -- Match on country code
    ON c.code = p.country_code
-- Focus on 2010
WHERE year = 2010
-- Order by life_exp
ORDER BY life_exp
-- Limit to 5 records
LIMIT 5;
```

### Comparing a country to itself
__________________________________
```sql
-- Select aliased fields from populations as p1
select p1.country_code,p1.size as size2010, p2.size as size2015
from populations as p1
-- Join populations as p1 to itself, alias as p2, on country code
inner join populations as p2
using(country_code)

SELECT 
	p1.country_code, 
    p1.size AS size2010, 
    p2.size AS size2015
FROM populations AS p1
INNER JOIN populations AS p2
ON p1.country_code = p2.country_code
WHERE p1.year = 2010
-- Filter such that p1.year is always five years before p2.year
    and p1.year = p2.year -5
```
