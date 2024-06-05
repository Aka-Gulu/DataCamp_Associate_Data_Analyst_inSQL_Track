### Numbering rows
__________________________
```sql
SELECT
  *,
  -- Assign numbers to each row
  row_number() over() AS Row_N
FROM Summer_Medals
ORDER BY Row_N ASC;
```

### Numbering Olympic games in ascending order
__________________________
```sql
SELECT
  Year,

  -- Assign numbers to each year
  row_number() over() AS Row_N
FROM (
  SELECT distinct year
  FROM Summer_Medals
  ORDER BY Year ASC
) AS Years
ORDER BY Year ASC;
```

### Numbering Olympic games in descending order
__________________________
```sql
SELECT
  Year,
  -- Assign the lowest numbers to the most recent years
  row_number() OVER (order by year desc) AS Row_N
FROM (
  SELECT DISTINCT Year
  FROM Summer_Medals
) AS Years
ORDER BY Year;
```

### Numbering Olympic athletes by medals earned
__________________________
```sql
SELECT
  -- Count the number of medals each athlete has earned
  distinct athlete,
  count(medal) AS Medals
FROM Summer_Medals
GROUP BY Athlete
ORDER BY Medals DESC;
/
WITH Athlete_Medals AS (
  SELECT
    -- Count the number of medals each athlete has earned
    Athlete,
    COUNT(*) AS Medals
  FROM Summer_Medals
  GROUP BY Athlete)

SELECT
  -- Number each athlete by how many medals they've earned
  Athlete,
  row_number() OVER (ORDER BY Medals DESC) AS Row_N
FROM Athlete_Medals
ORDER BY Medals DESC;
```
