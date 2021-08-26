---
title: "SQL Injections"
date: 2021-08-26
tags: [sql]
excerpt: "Reviewing some prior learnings."
---
Last year I wrote a [blog](https://www.inwt-statistics.com/read-blog/protecting-your-database-from-sql-injections.html) on preventing SQL injections while I was working with INWT Statistics in Berlin. This blog focuses on what SQL injections are, and how to prevent them through data sanitization and parameterized queries. 

In my current position, it's relatively common for me to create functions that use both SQL and Python. I am partial to f-strings in Python (I just think they're more legible), so I tend to gravitate towards those in my work. But it occurs to me that this is a SQL injection risk -- so it was good to review the above blog and make sure that I am properly parameterizing my queries!
