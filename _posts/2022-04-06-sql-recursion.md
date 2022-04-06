title: "Recursion in SQL"
date: 2022-04-06
tags: [sql]
excerpt: "Implementing recursive queries in SQL."
---

I have been working on improving my SQL skills, and I came across some questions that require recursion. I use SQL every day at work, but this one is new to me. So, here's a brief summary to consolidate my learning.

Pseudocode for the implementation in Postgresql:

```sql
WITH RECURSIVE cte_name AS (

	SELECT non_recursive_base_query

	UNION [ALL]

	SELECT recursive_query
	FROM cte_name
	WHERE [termination condition]
)

SELECT *
FROM cte_name
```

In this query, the base query runs first, then the recursive query using the output of the base query, then the recursive query again using the output from the last run of the recursive query, and so on until the termination condition is met.

Here's a simple example to print the numbers 1-10:

```sql
WITH RECURSIVE numbers AS (

	SELECT 1 AS n

	UNION

	SELECT n + 1
	FROM numbers WHERE n < 10 
)

SELECT *
FROM numbers
```
Note: Just looking at this it seems like it would print 1-9, since the `WHERE` clause says < 10, but on the last iteration n is less than 10 and then has 1 added to it.

Here's a harder question: Find the hierarchy of employees under a given manager (basically, direct reports and their reports). Let's say we have a table with employee_id, employee_name, and manager_id, and are looking for all the employees under the manager named 'Bob'.

```sql
WITH RECURSIVE employee_heirarchy AS (

	SELECT
		employee_id
		,employee_name
		,manager_id
	FROM employee_details
	WHERE employee_name = 'Bob'

	UNION

	SELECT
		r.employee_id
		,r.employee_name
		,r.manager_id
	FROM employee_heirarchy r
	JOIN employee_heirarchy b -- self-join with base query result
		ON b.employee_id = r.manager_id
)

SELECT *
FROM employee_heirarchy
```
