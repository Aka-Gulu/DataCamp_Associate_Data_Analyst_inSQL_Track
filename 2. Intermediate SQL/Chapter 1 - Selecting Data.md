### Practice with COUNT()
_________________________________________

```sql
-- Count the number of records in the people table
select count(*) as count_records from people

-- Count the number of birthdates in the people table
select count(birthdate) as count_birthdate from people

-- Count the records for languages and countries represented in the films table
select count(language)as count_languages,count(country) as count_countries from films 
```

### SELECT DISTINCT
_________________________________________
```sql
-- Return the unique countries from the films table
select distinct(country) from films

-- Count the distinct countries from the films table
select count(distinct(country)) as count_distinct_countries from films
```

### SELECT DISTINCT
_________________________________________
