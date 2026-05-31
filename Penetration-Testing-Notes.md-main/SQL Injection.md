A database is a way of electronically storing collections of data in an organized manner. A database is controlled by a DBMS, which is an acronymn for Database Management System.

Database Management System's has two camps
1. Relational - MySQL, Microsoft SQL Server, Access, PostgreSQL and SQL Lite
2. Non Relational - No SQL

A relational database stores information in tables, and often, the tables share information between them; they use columns to specify and define the data being stored and rows actually to store the data. The tables will often contain a column that has a unique ID (primary key), which will then be used in other tables to reference it and cause a relationship between the tables, hence the name relational database.

Non-relational databases, sometimes called NoSQL, on the other hand, are any sort of database that doesn't use tables, columns and rows to store the data. A specific database layout doesn't need to be constructed so each row of data can contain different information, giving more flexibility over a relational database.  Some popular databases of this type are MongoDB, Cassandra and ElasticSearch.

Tables : A table is made up of columns and rows; a useful way to imagine a table is like a grid with the columns going across the top from left to right containing the name of the cell and the rows going from top to bottom, with each one having the actual data.

Columns: Each column, better referred to as a field, has a unique name per table. When creating a column, you also set the type of data it will contain, common ones being integers (numbers), strings (standard text) or dates. Some databases can contain much more complex data, such as geospatial, which contains location information. Setting the data type also ensures that incorrect information isn't stored, such as the string "hello world" being stored in a column meant for dates. If this happens, the database server will usually produce an error message. A column containing an integer can also have an auto-increment feature enabled; this gives each row of data a unique number that grows (increments) with each subsequent row. Doing so creates what is called a key field; a key field has to be unique for every row of data, which can be used to find that exact row in SQL queries.

Rows: Rows or records contain individual lines of data. When you add data to the table, a new row/record is created; when you delete data, a row/record is removed.

SQL - SQL (Structured Query Language) is a feature-rich language used for querying databases.

SQL Syntax: 

SELECT: The first word SELECT, tells the database we want to retrieve some data; the * tells the database we want to receive back all columns from the table. 
for example, the table may contain three columns (id, username and password). "from users" tells the database we want to retrieve the data from the table named users. Finally, the semicolon at the end tells the database that this is the end of the query.  

Querries: 

select * from users;
select username,password from users;
select * from users LIMIT 1;
select * from users where username='admin';
select * from users where username != 'admin';
select * from users where username='admin' or username='jon';
select * from users where username='admin' and password='p4ssword';
select * from users where username like 'a%';
select * from users where username like '%n';
select * from users where username like '%mi%';

UNION:

The UNION statement combines the results of two or more SELECT statements to retrieve data from either single or multiple tables; the rules to this query are that the UNION statement must retrieve the same number of columns in each SELECT statement, the columns have to be of a similar data type, and the column order has to be the same.

SELECT name,address,city,postcode from customers UNION SELECT company,address,city,postcode from suppliers;

Insert: 

The INSERT statement tells the database we wish to insert a new row of data into the table. "into users" tells the database which table we wish to insert the data into, "(username,password)" provides the columns we are providing data for and then "values ('bob','password');" provides the data for the previously specified columns.

insert into users (username,password) values ('bob','password123');

UPDATE:

The UPDATE statement tells the database we wish to update one or more rows of data within a table. You specify the table you wish to update using "update %tablename% SET" and then select the field or fields you wish to update as a comma-separated list such as "username='root',password='pass123'" then finally, similar to the SELECT statement, you can specify exactly which rows to update using the where clause such as "where username='admin;".

update users SET username='root',password='pass123' where username='admin';

DELETE: 

The DELETE statement tells the database we wish to delete one or more rows of data. Apart from missing the columns you wish to return, the format of this query is very similar to the SELECT. You can specify precisely which data to delete using the where clause and the number of rows to be deleted using the LIMIT clause.

delete from users where username='martin';

**SQL Injection:**

The point wherein a web application using SQL can turn into SQL Injection is when user-provided data gets included in the SQL query.








































