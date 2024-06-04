## Querying the books table
_________________________________________
-- Return all titles from the books table
select title from books

-- Select title and author from the books table
SELECT title,author
FROM books;

-- Select all fields from the books table
SELECT *
FROM books;


## Making queries DISTINCT
_________________________________________
-- Select unique authors from the books table
select distinct author from books



-- Alias author so that it becomes unique_author
SELECT DISTINCT author as unique_author
FROM books;

_________________________________________

-- Save the results of this query as a view called library_authors
CREATE VIEW library_authors as
SELECT DISTINCT author AS unique_author
FROM books;

_________________________________________

-- Select the first 10 genres from books using PostgreSQL
select genre from books
limit 10

_________________________________________

