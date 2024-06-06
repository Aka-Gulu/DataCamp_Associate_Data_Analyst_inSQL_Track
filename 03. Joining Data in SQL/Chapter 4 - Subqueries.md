### Semi join
____________________________
```sql
-- Select country code for countries in the Middle East
Select code from countries
where region ='Middle East'

-- Select unique language names
select distinct name
FROM languages
-- Order by the name of the language
order by name;

SELECT DISTINCT name
FROM languages
-- Add syntax to use bracketed subquery below as a filter
where code in
    (SELECT code
    FROM countries
    WHERE region = 'Middle East')
ORDER BY name;
```

### Diagnosing problems using anti join
____________________________
```sql
-- Select code and name of countries from Oceania
select code,name from countries
where continent = 'Oceania'

SELECT code, name
FROM countries
WHERE continent = 'Oceania'
-- Filter for countries not included in the bracketed subquery
  and code not in
    (SELECT code
    FROM currencies);
```

### Subquery inside WHERE
____________________________
```sql
-- Select average life_expectancy from the populations table
select avg(life_expectancy) from populations
-- Filter for the year 2015
where year = 2015

SELECT *
FROM populations
-- Filter for only those populations where life expectancy is 1.15 times higher than average
WHERE life_expectancy > 1.15 *
  (SELECT AVG(life_expectancy)
   FROM populations
   WHERE year = 2015) 
    AND year = 2015;
```

### WHERE do people live?
____________________________
```sql
-- Select relevant fields from cities table
select name, country_code, urbanarea_pop from cities
-- Filter using a subquery on the countries table
where name in (select capital from countries)
ORDER BY urbanarea_pop DESC;
```

### Subquery inside SELECT
____________________________
```sql
-- Find top nine countries with the most cities
select countries.name as country, count(*) as cities_num from countries
left join cities on
countries.code = cities.country_code
group by country
-- Order by count of cities as cities_num
order by cities_num desc,country desc
limit 9;

SELECT countries.name AS country,
-- Subquery that provides the count of cities   
  (SELECT count(*)
   FROM cities
   WHERE countries.code=cities.country_code) AS cities_num
FROM countries
ORDER BY cities_num DESC, country
LIMIT 9;
```

### Subquery inside FROM
____________________________
```sql
-- Select code, and language count as lang_num
select code, count(*) as lang_num
from languages
group by code

-- Select local_name and lang_num from appropriate tables
select local_name, lang_num from countries, 
  (SELECT code, COUNT(*) AS lang_num
  FROM languages
  GROUP BY code) AS sub
-- Where codes match
where countries.code = sub.code
ORDER BY lang_num DESC;
```

### Subquery challenge
____________________________
```sql
-- Select relevant fields
SELECT code, inflation_rate, unemployment_rate
FROM economies
WHERE year = 2015 
  AND code NOT IN
-- Subquery returning country codes filtered on gov_form
    (SELECT code
     FROM countries
     WHERE (gov_form LIKE '%Monarchy%' OR gov_form LIKE '%Republic%'))
ORDER BY inflation_rate;
```

### Final challenge
____________________________
```sql
-- Select fields from cities
select name,country_code,city_proper_pop,metroarea_pop,(city_proper_pop/metroarea_pop*100) as city_perc from cities
-- Use subquery to filter city name
where name in (select capital from countries where continent ='Europe' or continent like '%America')
-- Add filter condition such that metroarea_pop does not have null values
and metroarea_pop is not null
-- Sort and limit the result
order by city_perc desc
limit 10;
```
