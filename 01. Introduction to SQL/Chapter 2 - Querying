### Querying the books table
_________________________________________

```sql
-- Return all titles from the books table
SELECT title FROM books;

-- Select title and author from the books table
SELECT title, author
FROM books;

-- Select all fields from the books table
SELECT *
FROM books;
```

### Making queries DISTINCT
_________________________________________
```sql
-- Select unique authors from the books table  
select distinct author from books

-- Select unique authors and genre combinations from the books table  
SELECT distinct author,genre
FROM books;
```

### Aliasing
_________________________________________
```sql
-- Alias author so that it becomes unique_author  
SELECT DISTINCT author as unique_author
FROM books;
```

### VIEWing your query
_________________________________________
```sql
-- Save the results of this query as a view called library_authors  
CREATE VIEW library_authors as
SELECT DISTINCT author AS unique_author
FROM books;

-- Your code to create the view:  
CREATE VIEW library_authors AS
SELECT DISTINCT author AS unique_author
FROM books;

-- Select all columns from library_authors  
select * from library_authors
```

### Limiting results
_________________________________________
```sql
-- Select the first 10 genres from books using PostgreSQL
select genre from books
limit 10
```
