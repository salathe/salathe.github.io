---
layout: post
title: T-SQL - Using a parameter in SELECT TOP clause
excerpt: Wrap the variable in parenthesis and magic...
---
I've been working with Stored Procedures a lot recently, even though the whole idea was new to be before starting this job. Today I was trying to select the first <code>n</code> rows from a table, but wanted to be able to change <code>n</code> via a parameter in the procedure call. I thought something like the following would work, but it throws an error when trying to use the variable for the TOP clause.

<pre lang="sql">CREATE PROCEDURE dbo.sp_TestGetAll
	@Limit int
AS
SELECT TOP @Limit
	id, col1, col2, col3
FROM TestTable</pre>

After a brief Google search there were a few methods presented including such nasties as manually writing out a SQL string within the procedure and executing that instead! Also, it was suggested to change the ROWCOUNT variable before and after issuing the select.  Sheesh, that's messy.

Well anyway, the solution which works for me and is much easier is simply to wrap the variable in parenthesis and magic... it works.

<pre lang="sql">CREATE PROCEDURE dbo.sp_TestGetAll
	@Limit int
AS
-- Notice the parentheses around @Limit - that is the only change!
SELECT TOP (@Limit) 
	id, col1, col2, col3
FROM TestTable</pre>

I'm just really noting this down so that I don't forget, do another Google search and then resort to those other ugly ways of doing this simple task.
