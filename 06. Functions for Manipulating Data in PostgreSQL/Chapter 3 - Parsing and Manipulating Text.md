### Concatenating strings
________________________________
```sql
-- Concatenate the first_name and last_name and email 
SELECT first_name || ' ' || last_name || ' <' || email || '>' AS full_email 
FROM customer

-- Concatenate the first_name and last_name and email
SELECT CONCAT(first_name, ' ', last_name, ' <', email, '>') AS full_email 
FROM customer
```

### Changing the case of string data
________________________________
```sql
SELECT 
  -- Concatenate the category name to coverted to uppercase
  -- to the film title converted to title case
  upper(c.name)  || ': ' || initcap(f.title) AS film_category, 
  -- Convert the description column to lowercase
  lower(f.description) AS description
FROM 
  film AS f 
  INNER JOIN film_category AS fc 
  	ON f.film_id = fc.film_id 
  INNER JOIN category AS c 
  	ON fc.category_id = c.category_id;
```

### Replacing string data
________________________________
```sql
SELECT 
  -- Replace whitespace in the film title with an underscore
  replace(title, ' ', '_') AS title
FROM film; 
```

### Determining the length of strings
________________________________
```sql
SELECT 
  -- Select the title and description columns
  title,
  description,
  -- Determine the length of the description column
  char_length(description) AS desc_len
FROM film;
```

### Truncating strings
________________________________
```sql
SELECT 
  -- Select the first 50 characters of description
  left(description, 50) AS short_desc
FROM 
  film AS f; 
  ```

### Extracting substrings from text data
________________________________
```sql
SELECT 
  -- Select only the street name from the address table
  substring(address from position(' ' in address)+1 FOR length(address))
FROM 
  address;
  ```

### Combining functions for string manipulation
________________________________
```sql
SELECT
  -- Extract the characters to the left of the '@'
  left(email, POSITION('@' IN email)-1) AS username,
  -- Extract the characters to the right of the '@'
  substring(email FROM POSITION('@' IN email)+1 for length(email)) AS domain
FROM customer;
```

### Padding
________________________________
```sql
-- Concatenate the padded first_name and last_name 
SELECT 
	RPAD(first_name, LENGTH(first_name)+1) || last_name AS full_name
FROM customer;

-- Concatenate the first_name and last_name 
SELECT 
	first_name || LPAD(last_name, LENGTH(last_name)+1) AS full_name
FROM customer;

-- Concatenate the first_name and last_name 
SELECT 
	RPAD(first_name, LENGTH(first_name)+1) 
    || RPAD(last_name, LENGTH(last_name)+2, ' <') 
    || RPAD(email, LENGTH(email)+1, '>') AS full_email
FROM customer;
```

### The TRIM function
________________________________
```sql
-- Concatenate the uppercase category name and film title
SELECT 
  concat(upper(c.name), ': ', f.title) AS film_category, 
  -- Truncate the description remove trailing whitespace
  trim(left(f.description, 50)) AS film_desc
FROM 
  film AS f 
  INNER JOIN film_category AS fc 
  	ON f.film_id = fc.film_id 
  INNER JOIN category AS c 
  	ON fc.category_id = c.category_id;
```

### Putting it all together
________________________________
```sql
SELECT 
  UPPER(c.name) || ': ' || f.title AS film_category, 
  -- Truncate the description without cutting off a word
  left(description, 50 - 
    -- Subtract the position of the first whitespace character
    position(
      ' ' IN REVERSE(LEFT(description, 50))
    )
  ) 
FROM 
  film AS f 
  INNER JOIN film_category AS fc 
  	ON f.film_id = fc.film_id 
  INNER JOIN category AS c 
  	ON fc.category_id = c.category_id;
```
