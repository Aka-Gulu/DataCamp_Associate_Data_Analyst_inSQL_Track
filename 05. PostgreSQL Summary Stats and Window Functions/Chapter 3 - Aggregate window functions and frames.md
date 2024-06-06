### Running totals of athlete medals
_______________________________
```sql
WITH Athlete_Medals AS (
  SELECT
    Athlete, COUNT(*) AS Medals
  FROM Summer_Medals
  WHERE
    Country = 'USA' AND Medal = 'Gold'
    AND Year >= 2000
  GROUP BY Athlete)

SELECT
  -- Calculate the running total of athlete medals
  Athlete,
  Medals,
  sum(Medals) OVER (ORDER BY Athlete ASC) AS Max_Medals
FROM Athlete_Medals
ORDER BY Athlete ASC;
```

### Maximum country medals by year
_______________________________
```sql
WITH Country_Medals AS (
  SELECT
    Year, Country, COUNT(*) AS Medals
  FROM Summer_Medals
  WHERE
    Country IN ('CHN', 'KOR', 'JPN')
    AND Medal = 'Gold' AND Year >= 2000
  GROUP BY Year, Country)

SELECT
  -- Return the max medals earned so far per country
  Country,
  Year,
  Medals,
  Max(Medals) OVER (PARTITION BY Country
                ORDER BY Year ASC) AS Max_Medals
FROM Country_Medals
ORDER BY Country ASC, Year ASC;
```

### Minimum country medals by year
_______________________________
```sql
WITH France_Medals AS (
  SELECT
    Year, COUNT(*) AS Medals
  FROM Summer_Medals
  WHERE
    Country = 'FRA'
    AND Medal = 'Gold' AND Year >= 2000
  GROUP BY Year)

SELECT
  Year,
  Medals,
  min(Medals) OVER (ORDER BY Year ASC) AS Min_Medals
FROM France_Medals
ORDER BY Year ASC;
```

### Moving maximum of Scandinavian athletes' medals
_______________________________
```sql
WITH Scandinavian_Medals AS (
  SELECT
    Year, COUNT(*) AS Medals
  FROM Summer_Medals
  WHERE
    Country IN ('DEN', 'NOR', 'FIN', 'SWE', 'ISL')
    AND Medal = 'Gold'
  GROUP BY Year)

SELECT
  -- Select each year's medals
  Year,
  Medals,
  -- Get the max of the current and next years'  medals
  max(Medals) OVER (ORDER BY Year ASC
             ROWS BETWEEN current row
             AND 1 following) AS Max_Medals
FROM Scandinavian_Medals
ORDER BY Year ASC;
```

### Moving maximum of Chinese athletes' medals
_______________________________
```sql
WITH Chinese_Medals AS (
  SELECT
    Athlete, COUNT(*) AS Medals
  FROM Summer_Medals
  WHERE
    Country = 'CHN' AND Medal = 'Gold'
    AND Year >= 2000
  GROUP BY Athlete)

SELECT
  -- Select the athletes and the medals they've earned
  Athlete,
  Medals,
  -- Get the max of the last two and current rows' medals 
  max(Medals) OVER (ORDER BY Athlete ASC
            ROWS BETWEEN 2 preceding
            AND current row) AS Max_Medals
FROM Chinese_Medals
ORDER BY Athlete ASC;
```

### Moving average of Russian medals
_______________________________
```sql
WITH Russian_Medals AS (
  SELECT
    Year, COUNT(*) AS Medals
  FROM Summer_Medals
  WHERE
    Country = 'RUS'
    AND Medal = 'Gold'
    AND Year >= 1980
  GROUP BY Year)

SELECT
  Year, Medals,
  --- Calculate the 3-year moving average of medals earned
  avg(Medals) OVER
    (ORDER BY Year ASC
     ROWS BETWEEN
     2 preceding AND current row) AS Medals_MA
FROM Russian_Medals
ORDER BY Year ASC;
```

### Moving total of countries' medals
_______________________________
```sql
WITH Country_Medals AS (
  SELECT
    Year, Country, COUNT(*) AS Medals
  FROM Summer_Medals
  GROUP BY Year, Country)

SELECT
  Year, Country, Medals,
  -- Calculate each country's 3-game moving total
  sum(Medals) OVER
    (PARTITION BY Country
     ORDER BY Year ASC
     ROWS BETWEEN
     2 preceding AND current row) AS Medals_MA
FROM Country_Medals
ORDER BY Country ASC, Year ASC;
```
