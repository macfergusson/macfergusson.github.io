## Learn SQL Server for free on your PC at home!

### Download SQL Server Developer Edition

[Microsoft Website](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)

Install that first.

### Download SSMS 

[Microsoft Website](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)

Install that second.

### Download a sample database 

[Brent Ozar's Stack Overflow](https://www.brentozar.com/archive/2015/10/how-to-download-the-stack-overflow-database-via-bittorrent/)

Follow the directions to attach the database.

Congratulations, you now have a full size database as your own personal sandbox to play in!

---

### Online Query Sharing

[db<>fiddle](https://dbfiddle.uk/)

[db-fiddle](https://www.db-fiddle.com/)

[sqlfiddle](http://sqlfiddle.com/)

---

### Basic Training Resources


[SQLBolt - Learn SQL](https://sqlbolt.com/)


[SQL Discord](https://discord.gg/5c5ge7a7Ku)


[SQL Server Training & Consulting for DBAs & Developers ](https://www.sqlskills.com/)


[How to Think Like the SQL Server Engine](https://www.youtube.com/playlist?list=PLDYqU5RH_aX1VSVvjdla9TOKf939UhIDB)


[Logical processing order of the SELECT statement - Microsoft](https://docs.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15#logical-processing-order-of-the-select-statement)

<img src="/images/Logical%20Processing%20Order.png">


---

### Suggested Beginner's Best Practices

* Use white space and line breaks to make your query easier to read.
* Specify the fields in your SELECT. Don't just rely on SELECT * to pull back every column in the table.
* Specify your JOIN types. INNER JOIN and LEFT OUTER are the two most common starting points.
* Specify the conditions of your JOIN with the ON statement. How are your two tables related to each other? Don't make us guess.
* Alias your tables to make your query easier to write and read. Aliases can be very short or descriptive.

```tsql
SELECT A.col1, A.col2, A.col3, B.col1, B.col2, B.col3
FROM TableA AS A
INNER JOIN TableB AS B
ON A.someID = B.someID;
```


### Other Sample Database Options

Most RDBMS come with some form of sample database. [Here is the documentation for Microsoft SQL Server](https://docs.microsoft.com/en-us/sql/samples/sql-samples-where-are?view=sql-server-ver15)

* [SqlZOO](https://sqlzoo.net/)
* [LearnSQL](https://learnsql.com/blog/ways-to-practice-sql-online/)
* [SQLSkills](https://www.sqlskills.com/sql-server-resources/sql-server-demos/)
* [Data repository collection](https://github.com/fivethirtyeight/data) at [FiveThirtyEight](https://data.fivethirtyeight.com/)
* [Kaggle has a lot of sample options](https://www.kaggle.com/)
* [Automated Github repository of various datasets](https://github.com/awesomedata/awesome-public-datasets)
