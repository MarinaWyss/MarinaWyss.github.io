---
title: "A Week of SQL"
date: 2020-06-04
tags: [sql]
header: 
  image: "/images/mysql/mysql.png"
excerpt: "Refreshing the basics, learning new things, and building a simple app with NodeJS."
---

## What I Learned: Lots of SQL!

This week I had originally planned to be visiting home in California, but given Covid-19, my vacation turned into a stay-cation, and I used the time to brush up on my SQL skills. 

I've taken a few courses before, and worked with basic SQL quite frequently at work. But there were a few things I was still a bit unsure about and wanted to make sure I really had down. 

So, I took another course that covered a review of the very basics to more advanced techniques, and culminated in creating a simple web app connecting to a MySQL database using NodeJS. I then went through the courses I've taken previously to make sure I didn't miss anything, and watched a few YouTube videos on outstanding concepts I was still unclear about. This post isn't a "blog" as much as a collection of notes, so that I work with the syntax again and have notes for the future!

**Data Types**

Not exhaustive - just some notes for things I wanted to remember here!

* CHAR: Fixed-length character (right-hand padded with spaces). When CHAR values are retrieved those trailing spaces are removed (unless otherwise specified). Faster than VARCHAR.
* VARCHAR: Variable-length character.
* DECIMAL: Is exact. Specified DECIMAL(max_digits, digits_after_dec) i.e. DECIMAL(5, 2) == 999.99.
* FLOAT and DOUBLE will store larger numbers using less space than DECIMAL, but are less precise than DECIMAL. FLOAT will have precision issues after about 7 digits, DOUBLE around 15 digits. 
* DATE: YYYY-MM-DD
* TIME: HH:MM:SS
* DATETIME: YYYY-MM-DD HH:MM:SS
* TIMESTAMP: DATETIME but limited to 1970-2038. Takes less memory.


**Database Basics**

```sql
CREATE DATABASE database_name;

DROP DATABASE database_name;

USE database_name; 

-- show currently-used db
SELECT database(); 
```

**Table Basics**

* Table names should be plural by convention. 

```sql
CREATE TABLE table_name
(
	column_name data_type constraints
	column_name data_type constraints
);

-- show all tables in db
SHOW TABLES;

-- table overviews
SHOW COLUMNS FROM table_name;
DESC table_name;


DROP TABLE table_name;

INSERT INTO table_name(column1, column2, ...)
VALUES (value1, value2, ...);

-- inserting multiple rows
INSERT INTO table_name(column)
VALUES (value), 
	   (value),
	   (value);
```

**Constraints**

Examples:

```sql
NOT NULL
DEFAULT
UNIQUE
AUTO_INCREMENT
PRIMARY KEY
FOREIGN KEY
CHECK
```

PRIMARY KEY is a unique identifier. Can be one or more columns (for example, if we wanted a unique combo of first and last name).

FOREIGN KEY references a PRIMARYKEY from another table. Convention is to name FOREIGN KEYs othertable_column. Will not allow you to include something in FOREIGN KEY that does not match to primary key on another table.

```sql
CREATE TABLE users
(
	id INT NOT NULL AUTO_INCREMENT,
	username VARCHAR(100) UNIQUE,
	age INT,
	PRIMARY KEY (id)
);

CREATE TABLE posts
(
	id INT NOT NULL AUTO_INCREMENT,
	user_id INT,
	PRIMARY KEY (id)
	FOREIGN KEY (user_id) REFERENCES users(id)
);

-- If we have a situation where we, say, want to delete all of a user's 
-- posts when we also delete the user, can use ON DELETE CASCADE 
-- when we create our FOREIGN KEY
CREATE TABLE posts
(
	id INT NOT NULL AUTO_INCREMENT,
	user_id INT,
	PRIMARY KEY (id)
	FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

CHECK 
Will check the data (big surprise) for some condition and not allow it to be inserted if it does not meet the condition. For example:

```sql
CREATE TABLE birthdays 
(
	birthday DATE CHECK (birthday > ‘1900-01-01’)
);

-- this will fail
INSERT INTO birthdays(birthday)
VALUES ('1899-11-11');
```

COALESCE
Accepts an unlimited number of arguments and returns the first that is not NULL. This could be useful if trying to do an operation on col with NULL values, and want to temporarily replace with 0 without altering data.

```sql
SELECT COALESCE (1, 2, 3) -- returns 1
SELECT COALESCE (NULL, 2, 3) -- returns 2

-- for example
SELECT 
	item, 
	(price - COALESCE(discount, 0)) AS final_price -- if discount is NULL, use 0 to calculate final_price
FROM table_name;
```

**Updating Tables**

We have two options: ALTER and UPDATE. ALTER changes the tables themselves, but not the data in the tables (unless we were to drop a column or something). UPDATE changes the rows in the table, but leaves the table itself unchanged.

```sql
-- UPDATE basic syntax
UPDATE table_name
SET column = changes
WHERE condition;

-- example
UPDATE users
SET age = 20
WHERE username = "someguy20";

-- ALTER basic syntax
ALTER TABLE table_name
changes;

-- examples
ALTER TABLE table_name
ADD COLUMN new_col;

ALTER TABLE old_table_name
RENAME TO new_table_name;

ALTER TABLE table_name
RENAME COLUMN col TO new_col_name;

ALTER TABLE table_name
DROP COLUMN IF EXISTS col;
```

**Deleting Data**

DELETE uses basically the same syntax as SELECT, except without the *

```sql
-- deletes rows but leaves table
DELETE FROM table_name;

-- more commonly use a WHERE
DELETE FROM table_name
WHERE condition;
```

**Selecting Data**

ORDER BY

```sql
-- alphanumeric
-- defaults to ascending order for descending
SELECT col 
FROM table_name
ORDER BY col DESC;

-- can use index of selected columns as a shortcut (but seems like a bad idea?)
SELECT col1, col2
FROM table_name
ORDER BY 2 -- will order by col2

-- order by more than one column. Will order by the first column then second, etc.
SELECT col1, col2
FROM table_name
ORDER BY col2, col1;
```

LIMIT

```sql
-- allows you to specify both starting and end point (indexing starts at 0)
SELECT col
FROM table_name
ORDER BY col
LIMIT 2, 10 for entries 2 to 10

-- can use bigger end number to grab just one:
SELECT col
FROM table_name
ORDER BY col
LIMIT 10, 1 -- will show item 11

-- retrieve all rows from some start number to end, use random giant number (really)
SELECT col
FROM table_name
LIMIT 5, 100000000000000000 -- will give us all entries after 6
```

WHERE/HAVING

```sql
-- WHERE goes before GROUP BY
SELECT COUNT(*)
FROM table_name
WHERE condition
GROUP BY something;

-- HAVING goes after
SELECT COUNT(*)
FROM table_name
GROUP BY something
HAVING condition;
```

BETWEEN/NOT BETWEEN

When using BETWEEN, it's best to use CAST to make sure data types are the time (i.e. both DATEs). 

```sql
SELECT *
FROM table_name
WHERE some_date between CAST(‘2000-01-01’ AS DATETIME) AND date_col;
```

IN/NOT IN

```sql
SELECT *
FROM table_name
WHERE query IN(‘item1’, ‘item2’, ‘item3’);
```

CASE 
```sql
-- will evaluate one line at a time, just like in R
SELECT *
	CASE
		WHEN condition THEN something
		WHEN condition THEN something_else
		ELSE last_thing
	END AS new_col_name
FROM table_name;
```

LIKE/NOT LIKE

```sql
-- % wildcard stands for anything before/after
SELECT name
FROM table_name
WHERE name LIKE '%something%';

-- LIKE is not case-sensitive. ILIKE in PostgreSQL is

-- use underscore to represent exact number of characters
SELECT qty
FROM table_name
WHERE qty LIKE ‘___’; -- where qty exactly 4 characters long

-- If searching for % or _ characters themselves, use a backslash: 
-- %\%% is searching for the %, 
-- %\_% is searching for _ in a string.
```

IFNULL

Will execute condition and replace NULL values.

```sql
IFNULL(condition, replace_with) 
IFNULL(SUM(amount), 0) -- will show sum(amount), if it’s NULL will return 0
```


Sub-Queries

```sql
-- say we want the title of the book that has the fewest pages:
SELECT title 
FROM books 
WHERE pages = (SELECT 
					MIN(pages) 
               FROM books); 

-- the second SELECT statement will execute first. Can be slow.

-- CTE (Common Table Expression) approach allows us to name a subquery and re-use it throughout
WITH <alias_name> AS (sql_subquery_statement)
SELECT column_list 
FROM <alias_name>[,table_name]
[WHERE <join_condition>];
```


**String Manipulation**

CONCAT

```sql
SELECT 
	CONCAT(col1, col2) 
FROM table_name;

-- include space
SELECT 
	CONCAT(col1, “ “, col2)
FROM table_name; 

-- “with symbol” so you don’t have to manually include the separator between all concatenations
SELECT
	CONCAT_WS(“-”, col1, col2, col3)
FROM table_name;

-- Can use || to concatenate in PostgreSQL and Oracle
```

SUBSTRING

```sql
-- grab a substring by index (indexing begins at 1)
SELECT 
	SUBSTRING(col, first_index, last_index);

-- from index to end of string
SELECT 
	SUBSTRING(col, first_index);

-- start counting from end of string
SELECT 
	SUBSTRING(col, negative_index);

-- SUBSTR() does the same thing

-- get the first n characters:
SELECT 
	LEFT(string, number_of_chars);

-- get the last n characters:
SELECT 
	RIGHT(string, number_of_chars);
```

REPLACE

```sql
-- case sensitive!
SELECT 
	REPLACE(col, ‘item_to_replace’, ‘replaced_with’)
FROM table_name;
```

REVERSE

```sql
-- same cases and spaces
SELECT
	REVERSE(column)
FROM table_name;
```

CHAR_LENGTH

```sql
SELECT 
	column
	CHAR_LENGTH(column)
FROM table_name;
```

UPPER/LOWER

```sql
SELECT
	UPPER(column)
FROM table_name;
```

Can combine string functions!

```sql
SELECT
	CONCAT
	(
		SUBSTRING(title, 1, 10),
		'...'
	) AS short_title
FROM books;
```

**Working with Dates**

* CURDATE: Current date
* CURTIME: Current time
* NOW: Current datetime
* DAY/MONTH/TIME: Will grab just some info from a DATE/TIME/DATETIME object.
* DAYNAME/MONTHNAME: Will take the written day/month
* DAYOFWEEK: Returns numeric day of week (Monday = 1, etc.)
* DAYOFYEAR: Is a numeric day of the year (takes into account things like leap years).
* Can use HOUR, MINUTE, SECOND etc. for TIME objects.

```sql
SELECT 
	DAYNAME(date) -- will return Monday, Tuesday, etc.
FROM table_name; 

-- alternatively, use EXTRACT
SELECT
	EXTRACT(TIME FROM time_col)
FROM table_name;
```

Formatting:
* DATE_FORMAT uses % specifiers. Can include -, /, etc. as desired between them. 
* TIME_FORMATTER also exists. 
* TO_CHAR to convert dt to text, must specify format.

```sql
SELECT 
	DATE_FORMAT(dt, '%m/%d/%Y %h:%i')
FROM table_name;

SELECT
	TO_CHAR(date_col, 'mm-dd-yyyy')
FROM table_name;
```

Date math:
* DATEDIFF: Number of days between two dates/dts
* AGE(date_col): Time since a timestamp to NOW()
* DATE_ADD & DATE_SUB: Can be used with lots of different intervals (seconds, quarters, etc.)
* Or, can just use +/-

```sql
SELECT 
	DATEDIFF(NOW(), some_date)
FROM table_name;

SELECT
	DATE_ADD(dt, INTERVAL 1 MONTH) 
FROM table_name;

SELECT 
	dt + INTERVAL 1 MONTH
FROM table_name;

-- with +/- can chain multiple together
SELECT 
	dt + INTERVAL 1 MONTH + INTERVAL 1 DAY
FROM table_name;
```

**Database Relationships**

* One:One relationships - 1 customer table and 1 customer details table.
* One:Many - 1 customer table, 1 order table (where one customer may have multiple orders).
* Many:Many relationships - An authors table and a books, where books can have any authors and authors can have many books.


**Joins**
* CROSS JOIN: Takes every row from table1 and repeats for every row of table2.
* INNER JOIN: Returns all rows where there is a match in both tables.
+ INTERSECT: Basically the same as INNER JOIN, but can return matching NULL values. INTERSECT also doesn't return any duplicate values.
* LEFT JOIN: Returns all rows of table1, and matches from table2 where they exist. Fills non-matches with NULL.
* RIGHT JOIN: Same thing, but all rows of table2. 
* OUTER JOIN: All rows from both tables, filling non-matches with NULL. 
* SELF JOIN: Basically using two copies of the same table. Must alias table names so they are uniquely identifiable.
* UNION: Allows us to "stack" two SELECTs for the same columns from different tables. Joins rows vs. columns.

```sql
-- basic syntax
SELECT *
FROM table1
JOIN table2
	ON table1.key = table2.key;

-- can string together multiple joins
SELECT 
    title,
    rating,
    CONCAT(first_name,' ', last_name) AS reviewer
FROM reviewers
INNER JOIN reviews 
    ON reviewers.id = reviews.reviewer_id
INNER JOIN series
    ON series.id = reviews.series_id
ORDER BY title;

-- self join
SELECT 
	table1.col, 
	table2.col
FROM table AS table1
JOIN table AS table2 
	ON table1.some_col = table2.other_col;

-- union
SELECT column FROM table1
UNION
SELECT column FROM table2;
```


**Views**

Views are a database object that is a stored query. Accessible as a virtual table. Doesn’t store data physically, just stores the query to it can be accessed quickly.

```sql
-- create view
CREATE VIEW view_name
some query;

-- access the view
SELECT * 
FROM view_name;

-- to update a view:
CREATE OR REPLACE VIEW view_name AS 
some new query; 

-- delete a view
DROP VIEW IF EXISTS view_name;

-- rename a view
ALTER VIEW view_name
RENAME TO new_view_name;
```


**Triggers**

SQL triggers specify code that is automatically run when a specific table is changed ("triggered" by certain events). For example, we could use a trigger to validate data like minimum age before inserting new data into a users table. 

Triggers are somewhat controversial, since these checks may be better suited to web app code, and they can make debugging challening. 

```sql
-- basic syntax
DELIMITER $$ 

CREATE TRIGGER trigger_name
	event_time event ON table FOR EACH ROW
	BEGIN
		Some SQL code to run
	END;

$$
DELIMITER ;
```
`DELIMITER $$` specifies that `$$` is our new end of line symbol. This is important so that all of the code below is treated as one chunk vs. multiple independent lines. `DELIMITER ;` at the end sets it back

```sql
-- example
DELIMITER $$ 

CREATE TRIGGER must_be_adult
	BEFORE INSERT ON users FOR EACH ROW
	BEGIN
		IF NEW.age < 18
		THEN 
			SIGNAL SQLSTATE '45000'
				SET MESSAGE_TEXT = 'Must be an adult'
		END IF;
	END;

$$
DELIMITER ;
```
In this example, `NEW.age` is placeholder data (we can also can use OLD.data). `SIGNAL SQLSTATE ‘45000’` references standardized SQL database error code. More info about these in the docs. 45000 is a generic user-defined error, not a specific MySQL error. 

Could also just log events with triggers:

```sql
-- when something is deleted from follows, something is added to the unfollows table

DELIMITER $$ 

CREATE TRIGGER create_unfollow
	AFTER DELETE ON follows FOR EACH ROW
	BEGIN
		INSERT INTO unfollows
		SET follower_id = OLD.follower_id,
		    followee_id = OLD.followee_id;
	END $$

DELIMITER ;
```
Some other basics:

```sql
-- show current triggers
SHOW TRIGGERS; 

-- delete a trigger
DROP TRIGGER trigger_name; 
```

**App with NodeJS**

As a final project, we put together a simple web app that takes in an email address, adds it to a MySQL database, and dynamically updates the front-end web form:

![image-center](/images/mysql/nodejs_mysql_app.gif){: .align-center}

```js
var express = require('express');
var app = express();
var mysql = require('mysql');
var bodyParser = require('body-parser');

app.set("view engine", "ejs");
app.use(bodyParser.urlencoded({extended: true}));
app.use(express.static(__dirname + '/public'));

var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'root',     
  database : 'join_us'   
});

app.get('/', function(req, res){
	var q = 'SELECT COUNT(*) as count FROM users';
	connection.query(q, function (error, results) {
		if (error) throw error;
		var count = results[0].count;
	res.render('home', {count: count});
 });
});

app.post('/register', function(req,res){
	var person = {email: req.body.email};
	connection.query('INSERT INTO users SET ?', person, function(err, result) {
		console.log(err);
		console.log(result);
		res.redirect('/');
 });
});
 
app.listen(3000, function () {
	console.log('App listening on port 3000!');
});
```


