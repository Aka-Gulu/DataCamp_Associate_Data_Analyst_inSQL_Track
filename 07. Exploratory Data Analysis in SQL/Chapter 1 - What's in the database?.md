### Count missing values
_______________________________
```sql
-- Select the count of the number of rows
SELECT count(*)
  FROM fortune500;

-- Select the count of ticker, 
-- subtract from the total number of rows, 
-- and alias as missing
SELECT count(*) - count(ticker) AS missing
  FROM fortune500;

-- Select the count of profits_change, 
-- subtract from total number of rows, and alias as missing
SELECT count(*) - count(profits_change) AS missing
  FROM fortune500;

-- Select the count of industry, 
-- subtract from total number of rows, and alias as missing
SELECT count(*) - count(industry) AS missing
  FROM fortune500;
```

### Join tables
_______________________________
```sql
SELECT company.name
-- Table(s) to select from
  FROM company
       inner join fortune500
       on company.ticker = fortune500.ticker;
```

### Read an entity relationship diagram
_______________________________
```sql
-- Count the number of tags with each type
SELECT type, count(*) AS count
  FROM tag_type
 -- To get the count for each type, what do you need to do?
 group by type
 -- Order the results with the most common
 -- tag types listed first
 order by count desc;

-- Select the 3 columns desired
SELECT company.name, tag_type.tag, tag_type.type
  FROM company
  	   -- Join to the tag_company table
       inner JOIN tag_company 
       ON company.id = tag_company.company_id
       -- Join to the tag_type table
       inner JOIN tag_type
       ON tag_company.tag = tag_type.tag
  -- Filter to most common type
  WHERE type='cloud';
```

### Coalesce
_______________________________
```sql
-- Use coalesce
SELECT coalesce(industry, sector, 'Unknown') AS industry2,
       -- Don't forget to count!
       count(*) 
  FROM fortune500
-- Group by what? (What are you counting by?)
 GROUP BY industry2
-- Order results to see most common first
 order by count(*) desc
-- Limit results to get just the one value you want
limit 1;
```

### Coalesce with a self-join
_______________________________
```sql
SELECT company_original.name, title,rank
  -- Start with original company information
  FROM company AS company_original
       -- Join to another copy of company with parent
       -- company information
	   LEFT JOIN company AS company_parent
       ON company_original.parent_id = company_parent.id 
       -- Join to fortune500, only keep rows that match
       inner JOIN fortune500 
       -- Use parent ticker if there is one, 
       -- otherwise original ticker
       ON coalesce(company_original.ticker, 
                   company_parent.ticker) = 
             fortune500.ticker
 -- For clarity, order by rank
 ORDER BY rank;
```

### Effects of casting
_______________________________
```sql
-- Select the original value
SELECT profits_change, 
	   -- Cast profits_change
       CAST(profits_change as integer) AS profits_change_int
  FROM fortune500;

-- Divide 10 by 3
SELECT 10/3, 
       -- Cast 10 as numeric and divide by 3
       10::numeric/3;

SELECT '3.2'::numeric,
       '-123'::numeric,
       '1e3'::numeric,
       '1e-3'::numeric,
       '02314'::numeric,
       '0002'::numeric;
  ```

### Summarize the distribution of numeric values
_______________________________
```sql
-- Select the count of each value of revenues_change
SELECT revenues_change, count(*)
  FROM fortune500
 group by revenues_change
 -- order by the values of revenues_change
 ORDER BY revenues_change;

-- Select the count of each revenues_change integer value
SELECT revenues_change::int, count(*)
  FROM fortune500
 group by revenues_change::int 
 -- order by the values of revenues_change
 ORDER BY revenues_change;

-- Count rows 
SELECT count(*)
  FROM fortune500
 -- Where...
 WHERE revenues_change > 0;
```
