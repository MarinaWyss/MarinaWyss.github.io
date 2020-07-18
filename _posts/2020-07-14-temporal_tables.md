---
title: "Temporal Tables in MariaDB"
date: 2020-07-14
tags: [sql]
excerpt: "A nifty way to easily handle system-versioned tables."
---

# What I Learned: All about temporal tables

To quote from the MariaDB docs: 

> System-versioned tables store the history of all changes, not only data which is currently valid. This allows data analysis for any point in time, auditing of changes and comparison of data from different points in time. Typical uses cases are:
>
> * Forensic analysis & legal requirements to store data for N years.
> * Data analytics (retrospective, trends etc.), e.g. to get your staff information as of one year ago.
> * Point-in-time recovery - recover a table state as of particular point in time.

The docs are quite good, so rather than repeating them here, I'll just [refer to them directly](https://mariadb.com/kb/en/temporal-data-tables/) in the future.

Today, my task was to figure out how to put older data into a MariaDB database with a timestamp (`row_start`) that makes sense for when they actually would have been valid. There seem to be a few work-arounds for this with SQL Server (essentially turning system versioning off, making the inserts, and turning it back on again), but this approach doesn't work for MariaDB, as the system versioning is deleted when it's dropped. I'm still looking into options here, but definitely learned a lot about how these tables work in the process. 

