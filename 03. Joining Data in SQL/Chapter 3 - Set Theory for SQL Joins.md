### Comparing global economies
__________________________________
```sql
-- Select all fields from economies2015
select * from economies2015    
-- Set operation
union       
-- Select all fields from economies2019
select * from economies2019
ORDER BY code, year;
```

### Comparing two set operations
__________________________________
```sql
-- Query that determines all pairs of code and year from economies and populations, without duplicates
select code, year from economies
union
select country_code, year from populations
order by code,year

SELECT code, year
FROM economies
-- Set theory clause
UNION all
SELECT country_code, year
FROM populations
ORDER BY code, year;
```

### INTERSECT
__________________________________
```sql
-- Return all cities with the same name as a country
select name from cities
intersect
select name from countries
```

### You've got it, EXCEPT...
__________________________________
```sql
-- Return all cities that do not have the same name as a country
select name from cities
except
select name from countries
ORDER BY name;
```
