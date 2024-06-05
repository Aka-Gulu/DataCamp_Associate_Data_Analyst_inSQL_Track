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

### Reigning weightlifting champions
__________________________
```sql
SELECT
  -- Return each year's champions' countries
  year,
  country AS champion
FROM Summer_Medals
WHERE
  Discipline = 'Weightlifting' AND
  Event = '69KG' AND
  Gender = 'Men' AND
  Medal = 'Gold';

WITH Weightlifting_Gold AS (
  SELECT
    -- Return each year's champions' countries
    Year,
    Country AS champion
  FROM Summer_Medals
  WHERE
    Discipline = 'Weightlifting' AND
    Event = '69KG' AND
    Gender = 'Men' AND
    Medal = 'Gold')

SELECT
  Year, Champion,
  -- Fetch the previous year's champion
  lag(Champion,1) OVER
    (order by year ASC) AS Last_Champion
FROM Weightlifting_Gold
ORDER BY Year ASC;
```

### Reigning champions by gender
__________________________
```sql
WITH Tennis_Gold AS (
  SELECT DISTINCT
    Gender, Year, Country
  FROM Summer_Medals
  WHERE
    Year >= 2000 AND
    Event = 'Javelin Throw' AND
    Medal = 'Gold')

SELECT
  Gender, Year,
  Country AS Champion,
  -- Fetch the previous year's champion by gender
  lag(Country,1) OVER (partition by gender
            ORDER BY gender ASC) AS Last_Champion
FROM Tennis_Gold
ORDER BY Gender ASC, Year ASC;
```

### Reigning champions by gender and event
__________________________
```sql
WITH Athletics_Gold AS (
  SELECT DISTINCT
    Gender, Year, Event, Country
  FROM Summer_Medals
  WHERE
    Year >= 2000 AND
    Discipline = 'Athletics' AND
    Event IN ('100M', '10000M') AND
    Medal = 'Gold')

SELECT
  Gender, Year, Event,
  Country AS Champion,
  -- Fetch the previous year's champion by gender and event
  lag(Country,1) OVER (partition by gender,event
            ORDER BY Year ASC) AS Last_Champion
FROM Athletics_Gold
ORDER BY Event ASC, Gender ASC, Year ASC;
```

