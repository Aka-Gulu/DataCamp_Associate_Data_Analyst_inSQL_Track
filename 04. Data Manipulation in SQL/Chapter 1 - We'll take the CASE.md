### Basic CASE statements
____________________________
```sql
SELECT
	-- Select the team long name and team API id
	team_long_name,team_api_id
	
FROM teams_germany
-- Only include FC Schalke 04 and FC Bayern Munich
WHERE team_long_name in ('FC Schalke 04', 'FC Bayern Munich');

-- Identify the home team as Bayern Munich, Schalke 04, or neither
SELECT 
	case when hometeam_id = 10189 then 'FC Schalke 04'
        when hometeam_id = 9823 then 'FC Bayern Munich'
         ELSE 'Other' END AS home_team,
	COUNT(id) AS total_matches
FROM matches_germany
-- Group by the CASE statement alias
GROUP BY home_team;
```

### CASE statements comparing column values
____________________________
```sql
SELECT 
	-- Select the date of the match
	date,
	-- Identify home wins, losses, or ties
	case when home_goal > away_goal then 'Home win!'
        when home_goal < away_goal then 'Home loss :(' 
        else 'Tie' end as outcome
FROM matches_spain;

SELECT 
	m.date,
	--Select the team long name column and call it 'opponent'
	t.team_long_name AS opponent, 
	-- Complete the CASE statement with an alias
	case when m.home_goal > m.away_goal then 'Home win!'
        when m.home_goal < m.away_goal then 'Home loss :('
        else 'Tie' end as outcome
FROM matches_spain AS m
-- Left join teams_spain onto matches_spain
left join teams_spain AS t
on m.awayteam_id = t.team_api_id;

SELECT 
	m.date,
	t.team_long_name AS opponent,
    -- Complete the CASE statement with an alias
	case when m.home_goal > m.away_goal then 'Barcelona win!'
        when m.home_goal < m.away_goal then 'Barcelona loss :(' 
        else 'Tie' end as outcome 
FROM matches_spain AS m
LEFT JOIN teams_spain AS t 
ON m.awayteam_id = t.team_api_id
-- Filter for Barcelona as the home team
WHERE m.hometeam_id = 8634;
```

### CASE statements comparing two column values part 2
____________________________
```sql
-- Select matches where Barcelona was the away team
SELECT  
	m.date,
	t.team_long_name AS opponent,
	case when home_goal < away_goal then 'Barcelona win!'
        WHEN home_goal > away_goal then 'Barcelona loss :(' 
        else 'Tie' end as outcome
FROM matches_spain AS m
-- Join teams_spain to matches_spain
LEFT JOIN teams_spain AS t 
ON m.hometeam_id = t.team_api_id
WHERE m.awayteam_id = 8634;
```

### In CASE of rivalry
____________________________
```sql
SELECT 
	date,
	-- Identify the home team as Barcelona or Real Madrid
	CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END AS home,
    -- Identify the away team as Barcelona or Real Madrid
	CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END AS away
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
      AND (awayteam_id = 8633 OR hometeam_id = 8633);

SELECT 
	date,
	CASE WHEN hometeam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as home,
	CASE WHEN awayteam_id = 8634 THEN 'FC Barcelona' 
         ELSE 'Real Madrid CF' END as away,
	-- Identify all possible match outcomes
	case when home_goal > away_goal and hometeam_id = 8634 then 'Barcelona win!'
        WHEN home_goal > away_goal and hometeam_id = 8633 then 'Real Madrid win!'
        WHEN home_goal < away_goal and awayteam_id = 8634 then 'Barcelona win!'
        WHEN home_goal < away_goal and awayteam_id = 8633 then 'Real Madrid win!'
        else 'Tie!' end as outcome
FROM matches_spain
WHERE (awayteam_id = 8634 OR hometeam_id = 8634)
      AND (awayteam_id = 8633 OR hometeam_id = 8633);
```

### Filtering your CASE statement
____________________________
```sql
-- Select team_long_name and team_api_id from team
SELECT
	team_long_name,
	team_api_id
FROM teams_italy
-- Filter for team long name
WHERE team_long_name = 'Bologna';

-- Select the season and date columns
SELECT 
	season,
	date,
    -- Identify when Bologna won a match
	case when hometeam_id = 9857 
        and home_goal > away_goal 
        then 'Bologna Win'
		when awayteam_id = 9857 
        and away_goal > home_goal 
        then 'Bologna Win' 
		end AS outcome
FROM matches_italy;

-- Select the season, date, home_goal, and away_goal columns
SELECT 
	season,
    date,
	home_goal,
	away_goal
FROM matches_italy
WHERE 
-- Exclude games not won by Bologna
	case when hometeam_id = 9857 and home_goal > away_goal then 'Bologna Win'
		when awayteam_id = 9857 and away_goal > home_goal then 'Bologna Win' 
		end IS NOT null;
```

### COUNT using CASE WHEN
____________________________
```sql
SELECT 
	c.name AS country,
    -- Count games from the 2012/2013 season
	count(case when m.season = '2012/2013' 
        	then m.id ELSE null end) AS matches_2012_2013
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
group by  country;

SELECT 
	c.name AS country,
    -- Count matches in each of the 3 seasons
	count(case when m.season =  '2012/2013' then m.id end) AS matches_2012_2013,
	count(case when m.season = '2013/2014' then m.id end) AS matches_2013_2014,
	count(case when m.season = '2014/2015' then m.id end) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
group by  country;
```

### COUNT and CASE WHEN with multiple conditions
____________________________
```sql
SELECT 
	c.name AS country,
    -- Sum the total records in each season where the home team won
	sum(case when m.season = '2012/2013' AND m.home_goal > m.away_goal 
        THEN 1 ELSE 0 end) AS matches_2012_2013,
 	sum(case when m.season = '2013/2014' and m.home_goal > m.away_goal 
        THEN 1 else 0 end) AS matches_2013_2014,
	sum(case when m.season = '2014/2015' and m.home_goal > m.away_goal 
        then 1 else 0 end) AS matches_2014_2015
FROM country AS c
LEFT JOIN match AS m
ON c.id = m.country_id
-- Group by country name alias
GROUP BY country;
```

### Calculating percent with CASE and AVG
____________________________
```sql
SELECT 
    c.name AS country,
    -- Count the home wins, away wins, and ties in each country
	count(case when m.home_goal > m.away_goal THEN m.id 
        END) AS home_wins,
	count(case when m.home_goal < m.away_goal THEN m.id 
        END) AS away_wins,
	count(case when m.home_goal = m.away_goal THEN m.id 
        END) AS ties
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;

SELECT 
	c.name AS country,
    -- Calculate the percentage of tied games in each season
	avg(case when m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
			WHEN m.season='2013/2014' AND m.home_goal <> m.away_goal THEN 0
			END) AS ties_2013_2014,
	avg(case when m.season='2014/2015' and m.home_goal = m.away_goal then 1
			WHEN m.season='2014/2015' and m.home_goal != m.away_goal then 0
			end) as ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;

SELECT 
	c.name AS country,
    -- Round the percentage of tied games to 2 decimal points
	Round(avg(CASE WHEN m.season='2013/2014' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2013/2014' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2013_2014,
	round(avg(CASE WHEN m.season='2014/2015' AND m.home_goal = m.away_goal THEN 1
			 WHEN m.season='2014/2015' AND m.home_goal != m.away_goal THEN 0
			 END),2) AS pct_ties_2014_2015
FROM country AS c
LEFT JOIN matches AS m
ON c.id = m.country_id
GROUP BY country;
```
